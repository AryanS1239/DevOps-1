pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'aryansonawane1239/devops-assignment:latest'
        DOCKER_CREDENTIALS_ID = 'dockerhub-login' 
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image..."
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        stage('Push to Registry') {
            steps {
                echo "Pushing Image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying to Minikube Cluster via Docker..."
                sh "docker cp k8s-deployment.yaml minikube:/tmp/k8s-deployment.yaml"
                sh """
                docker exec -u root minikube sh -c 'curl -sLO https://dl.k8s.io/release/v1.29.2/bin/linux/amd64/kubectl && chmod +x kubectl && mv kubectl /usr/local/bin/ && kubectl --kubeconfig=/etc/kubernetes/admin.conf apply -f /tmp/k8s-deployment.yaml'
                """
            }
        }
    }
}