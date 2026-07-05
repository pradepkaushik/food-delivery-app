pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Checking out source code from GitHub...'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t food-app:latest .'
            }
        }

    }
}