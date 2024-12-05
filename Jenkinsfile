pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mynginx/webapp'
        DOCKER_HUB_USERNAME = 'your-dockerhub-username'
        DOCKER_HUB_PASSWORD = credentials('docker-hub-password-id')  // Create Jenkins credentials for Docker Hub
        GITHUB_REPO = 'https://github.com/your-username/your-repository.git'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "$GITHUB_REPO", branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the NGINX Docker image
                    docker.build("$DOCKER_IMAGE", "-f Dockerfile .")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image("$DOCKER_IMAGE").push()
                    }
                }
            }
        }

        stage('Deploy to Docker') {
            steps {
                script {
                    // Stop and remove the existing container (if any)
                    sh 'docker rm -f webapp-container || true'

                    // Run the new Docker container
                    sh 'docker run -d -p 80:80 --name webapp-container $DOCKER_IMAGE'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline successfully executed.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

