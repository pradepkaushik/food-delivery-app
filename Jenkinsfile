pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Checking out code from GitHub...'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t food-app:latest .
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker rm -f food-app-container || true
                    docker run -d --name food-app-container -p 80:80 food-app:latest
                '''
            }
        }

        stage('Verify') {
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
            echo '✅ Application deployed successfully!'
        }

        failure {
            echo '❌ Pipeline failed.'
        }
    }
}
