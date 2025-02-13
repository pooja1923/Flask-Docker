pipeline {
    agent any

    environment {
        IMAGE_NAME = "poojak19/flask-docker-app"
        CONTAINER_NAME = "flask-container"
        DOCKER_TAG = "latest"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials" 
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/pooja1923/Flask-Docker.git'  
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t %IMAGE_NAME%:%DOCKER_TAG% .'  
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: DOCKER_CREDENTIALS_ID, url: 'https://index.docker.io/v1/']) {
                        bat 'docker push %IMAGE_NAME%:%DOCKER_TAG%'
                    }
                }
            }
        }

       stage('Run New Container') {
            steps {
                script {
                    bat 'docker run -d -p 5000:5000 --name %CONTAINER_NAME% %IMAGE_NAME%:%DOCKER_TAG%'
                }
            }
        }

       stage('Cleanup') {
    steps {
        script {
            bat '''
            docker system prune -f
            FOR /F "tokens=*" %%i IN ('docker images -f "dangling=true" -q') DO docker rmi %%i || echo "No dangling images to remove"
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
