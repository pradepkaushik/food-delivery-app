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
                    set -e
                    docker build -t food-app:latest .
                '''
            }
        }

        stage('Deploy to Nginx') {
            steps {
                sh '''
                    set -e

                    echo "Removing old website files..."
                    sudo rm -rf /var/www/html/*

                    echo "Copying new website files..."
                    sudo cp -r /var/lib/jenkins/workspace/first-pipeline/* /var/www/html/

                    echo "Deployment completed."
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    echo "Listing files in Nginx directory:"
                    ls -la /var/www/html

                    echo "Testing local web server:"
                    curl http://localhost
                '''
            }
        }
    }
}
