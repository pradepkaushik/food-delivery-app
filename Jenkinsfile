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

                echo "Applying Kubernetes Deployment..."
                kubectl apply -f k8/Deployment.yaml

                # Uncomment this if you have a Service.yaml
                # kubectl apply -f k8/Service.yaml

                echo "Restarting Deployment..."
                kubectl rollout restart deployment/${DEPLOYMENT_NAME}

                echo "Waiting for rollout..."
                kubectl rollout status deployment/${DEPLOYMENT_NAME}

                echo "----------------------------------------"
                kubectl get deployments

                echo "----------------------------------------"
                kubectl get pods -o wide

                echo "----------------------------------------"
                kubectl get svc
            '''
        }
    }
}
