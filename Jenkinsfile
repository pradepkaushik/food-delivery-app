pipeline {
    agent any

    environment {
        IMAGE_NAME = "food-app"
        CONTAINER_NAME = "food-app-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Checking out source code...'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p 80:80 \
                        ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    docker ps
                    curl http://localhost
                '''
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
