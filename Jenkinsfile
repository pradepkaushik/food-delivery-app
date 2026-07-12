```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "food-app"
        CONTAINER_NAME = "food-app-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Checking out code from GitHub...'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    set -e
                    docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    set +e
                    docker rm -f ${CONTAINER_NAME}
                    set -e

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
                    echo "Running Containers:"
                    docker ps

                    echo "Testing Application:"
                    curl http://localhost
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Application deployed successfully!'
        }

        failure {
            echo '❌ Pipeline failed.'
        }
    }
}
```
