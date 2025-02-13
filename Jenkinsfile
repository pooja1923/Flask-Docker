pipeline {
    agent any

    environment {
        IMAGE_NAME = "poojak19/flask-docker-app"
        CONTAINER_NAME = "flask-container"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials" 
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/pooja1923/Flask-Docker.git'  
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t %IMAGE_NAME% .'  
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    bat 'docker push %IMAGE_NAME%'
                }
            }
        }

      stage('Run Container') {
            steps {
                script {
                    bat 'docker run -d -p 5000:5000 --name %CONTAINER_NAME% %IMAGE_NAME%'
                }
            }
        }

        stage('Cleanup Unused Docker Resources') {
            steps {
                script {
                    bat '''
                    docker system prune -f
                    docker rmi $(docker images -f "dangling=true" -q) 2>nul || echo "No dangling images to remove"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Build or deployment failed!"
        }
    }
}
