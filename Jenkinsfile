pipeline {
    agent any

    environment {
        IMAGE_NAME = "ankemsaichaitanya/kubeapp"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Clone Repository') {
            steps {
                // Jenkins does Git checkout automatically
                git branch: 'main', url: 'https://github.com/ankemsaichaitanya/kube-devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🛠️ Building Docker image...'
                bat '''
                docker build -t %IMAGE_NAME%:%IMAGE_TAG% .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo '📦 Pushing Docker image to Docker Hub...'
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_PASS')]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u ankemsaichaitanya --password-stdin
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo '☸️ Deploying to Kubernetes...'
                bat '''
                kubectl apply -f my-kube1-deployment.yaml
                kubectl apply -f my-kube1-service.yaml
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo '🔍 Verifying Deployment...'
                bat '''
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Build Failed. Please check console output.'
        }
    }
}
