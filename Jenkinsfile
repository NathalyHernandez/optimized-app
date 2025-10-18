pipeline {
    agent any

    environment {
        // Define environment variables here
        REGISTRY_CENTRAL_USER = 'centralcontainers'
        REGISTRY_CENTRAL_HOST = "${REGISTRY_CENTRAL_USER}.azurecr.io"
        IMAGE_NAME = "${REGISTRY_CENTRAL_HOST}/optimized-app:${env.BUILD_NUMBER}"
        RESOURCE_GROUP = 'rg-devops-test'
        AKS_CLUSTER = 'aks-devops-test'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'docker run --rm ${IMAGE_NAME} node -e "console.log(\'Container is working!\')"'
            }
        }
        stage('Push to ACR') {
            steps {
                echo 'Pushing to ACR...'
                sh 'az acr login --name ${REGISTRY_CENTRAL_USER}'
                sh 'docker push ${IMAGE_NAME}'
            }
        }
        stage('Deploy to AKS') {
            steps {
                echo 'Deploying to AKS...'
                sh 'az aks get-credentials --resource-group ${RESOURCE_GROUP} --name ${AKS_CLUSTER}'
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl rollout status deployment/optimized-app'
                // Add deploy steps here
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}