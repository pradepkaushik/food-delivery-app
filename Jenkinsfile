pipeline {
    agent any

    environment {

        // AWS Configuration
        AWS_REGION        = "ap-south-1"
        AWS_ACCOUNT_ID    = "596055752329"

        // Jenkins AWS Credential ID
        AWS_CREDENTIAL_ID = "food-app"

        // Amazon ECR
        ECR_REPOSITORY    = "food-app"
        IMAGE_TAG         = "latest"

        // Amazon EKS
        EKS_CLUSTER       = "food-app"

        // Kubernetes Deployment
        DEPLOYMENT_NAME   = "food-app"

        // Docker Image
        IMAGE_URI = "596055752329.dkr.ecr.ap-south-1.amazonaws.com/food-app:latest"
    }

    stages {

        stage('Checkout Source') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh '''
                    docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Login to Amazon ECR') {
            steps {

                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: "${AWS_CREDENTIAL_ID}"
                ]]) {

                    sh '''
                        aws ecr get-login-password \
                        --region ${AWS_REGION} | \
                        docker login \
                        --username AWS \
                        --password-stdin \
                        ${AWS_ACCOUNT_ID}.dkr.ecr.ap-south-1.amazonaws.com
                    '''
                }
            }
        }

        stage('Tag Docker Image') {
            steps {

                sh '''
                    docker tag \
                    ${ECR_REPOSITORY}:${IMAGE_TAG} \
                    ${IMAGE_URI}
                '''
            }
        }

        stage('Push Image to Amazon ECR') {
            steps {

                sh '''
                    docker push ${IMAGE_URI}
                '''
            }
        }

        stage('Deploy to Amazon EKS') {
            steps {

                echo 'Deploying application to Amazon EKS...'

                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: "${AWS_CREDENTIAL_ID}"
                ]]) {

                    sh '''
                        set -e

                        echo "Updating kubeconfig..."
                        aws eks update-kubeconfig \
                            --region ${AWS_REGION} \
                            --name ${EKS_CLUSTER}

                        echo "Applying Deployment..."
                        kubectl apply -f k8/Deployment.yaml

                        # Uncomment if Service.yaml exists
                        # kubectl apply -f k8/Service.yaml

                        echo "Restarting Deployment..."
                        kubectl rollout restart deployment/${DEPLOYMENT_NAME}

                        echo "Waiting for Rollout..."
                        kubectl rollout status deployment/${DEPLOYMENT_NAME}

                        echo "--------------------------------------"
                        kubectl get deployments

                        echo "--------------------------------------"
                        kubectl get pods -o wide

                        echo "--------------------------------------"
                        kubectl get svc
                    '''
                }
            }
        }

    }

    post {

        success {
            echo "Application successfully deployed to Amazon EKS."
        }

        failure {
            echo "Pipeline failed."
        }

        always {
            sh '''
                docker image prune -f || true
            '''
        }
    }
}
