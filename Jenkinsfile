pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        AWS_ACCOUNT_ID = "596055752329"
        ECR_REPO = "food-app"
        IMAGE_NAME = "food-app"
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
                    docker build -t food-app:latest .
                '''
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                    set -e

                    echo "Getting ECR login token..."
                    aws ecr get-login-password --region ap-south-1 > /tmp/ecr_token.txt

                    echo "Logging into ECR..."
                    docker login --username AWS --password-stdin 596055752329.dkr.ecr.ap-south-1.amazonaws.com < /tmp/ecr_token.txt
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                    set -e
                    docker tag food-app:latest 596055752329.dkr.ecr.ap-south-1.amazonaws.com/food-app:latest
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    set -e
                    docker push 596055752329.dkr.ecr.ap-south-1.amazonaws.com/food-app:latest
                '''
            }
        }
    }
}