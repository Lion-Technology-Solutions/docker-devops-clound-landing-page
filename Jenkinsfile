pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = 'liontech/liontech-academy'
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Lion-Technology-Solutions/docker-liontech-academy-full-website.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${TAG}", ".")
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Add your test commands here
                    sh 'echo "Running tests..."'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image("${IMAGE_NAME}:${TAG}").push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // For production deployment
                    sh 'docker-compose down && docker-compose up -d'
                    
                    // Or for Kubernetes deployment
                    // sh 'kubectl apply -f k8s-deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }
        success {
            // Notification for successful build
            slackSend(color: 'good', message: "Build ${env.BUILD_NUMBER} succeeded")
        }
        failure {
            // Notification for failed build
            slackSend(color: 'danger', message: "Build ${env.BUILD_NUMBER} failed")
        }
    }
}