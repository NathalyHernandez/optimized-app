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
                // Add test steps here
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
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