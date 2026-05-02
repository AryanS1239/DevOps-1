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
            steps {
                // Tells Kubernetes to apply your deployment configurations
                echo "Deploying to Minikube Cluster..."
                sh "kubectl apply -f k8s-deployment.yaml"
            }
        }
    }
}