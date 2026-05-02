pipeline {
    agent any
    environment {
        // Defines the image name and the ID we'll use for credentials in Jenkins
        DOCKER_IMAGE = 'aryansonawane1239/devops-assignment:latest'
        DOCKER_CREDENTIALS_ID = 'dockerhub-login' 
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Pulls the latest code from your GitHub repository
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                // Uses the Docker socket we attached earlier to build the image
                echo "Building Docker Image..."
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        stage('Push to Registry') {
            steps {
                // Logs into Docker Hub and pushes the newly built image
                echo "Pushing Image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Deploy to Kubernetes') {
           stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying to Minikube Cluster via Docker..."
                // 1. Copy the YAML file into Minikube
                sh "docker cp k8s-deployment.yaml minikube:/tmp/k8s-deployment.yaml"
                
                // 2. Run as ROOT (-u root), move kubectl to a trusted folder, and apply
                sh """
                docker exec -u root minikube sh -c 'curl -sLO https://dl.k8s.io/release/v1.29.2/bin/linux/amd64/kubectl && chmod +x kubectl && mv kubectl /usr/local/bin/ && kubectl --kubeconfig=/etc/kubernetes/admin.conf apply -f /tmp/k8s-deployment.yaml'
                """
            }
        }
    }
}