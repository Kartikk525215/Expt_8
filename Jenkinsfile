pipeline {
    agent any

    environment {
        IMAGE_NAME = "swapnilk10/my-html-site"   // DockerHub username + repo/project name
        IMAGE_TAG  = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "📥 Checking out code from Kartik's GitHub repo..."
                git branch: 'main', url: 'https://github.com/Kartikk525215/Expt_8.git'
            }
        }

        stage('Check Docker Status') {
            steps {
                echo "🔍 Checking if Docker is running..."
                bat """
                    docker info || (echo "❌ Docker daemon not running! Please start Docker Desktop and restart Jenkins." && exit 1)
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo "🔐 Logging in to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'docker-project', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                        docker logout || echo Already logged out
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🏗️ Building Docker image..."
                bat """
                    docker build -t %IMAGE_NAME%:%IMAGE_TAG% ./my-html-site
                """
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                echo "📤 Pushing image to Docker Hub..."
                bat """
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('Deploy Container') {
            steps {
                echo "🚀 Deploying container..."
                bat """
                    docker rm -f web-container || echo Container not running
                    docker run -d --name web-container -p 7070:80 %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }
    }

    post {
        always {
            echo "🧹 Logging out of Docker Hub..."
            bat 'docker logout || echo Already logged out'
            echo "✅ Pipeline finished successfully. Visit: http://localhost:7070"
        }
    }
}
