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
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-cred']]) {
                    sh '''
                        set -e

                        echo "Testing AWS identity..."
                        aws sts get-caller-identity

                        echo "Logging into ECR..."
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                    '''
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                    set -e
                    docker tag food-app:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:latest
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    set -e
                    docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:latest
                '''
            }
        }
    }
}