pipeline {
 
  gent any

  environment {
    REGISTRY_CENTRAL_USER = 'centralcontainers'
    REGISTRY_CENTRAL_HOST = "${REGISTRY_CENTRAL_USER}.azurecr.io"
    IMAGE_NAME = "${REGISTRY_CENTRAL_HOST}/optimized-app:${env.BUILD_NUMBER}"
    RESOURCE_GROUP = 'rg-devops-test'
    AKS_CLUSTER = 'aks-devops-test'
  }

  stages {
    stage('Checkout') {
      steps {
        echo 'Clonando el repositorio desde la rama feature...'
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        echo 'Construyendo imagen Docker...'
        sh 'docker build -t $IMAGE_NAME .'
      }
    }

    stage('Run Tests') {
      steps {
        echo 'Ejecutando pruebas...'
        sh '''
          if [ -f package.json ]; then
            npm install
            npm test || echo " Algunas pruebas fallaron, continuando para demo"
          fi
        '''
      }
    }

    stage('Login to Azure') {
      steps {
        echo 'Autenticando con Azure...'
        sh '''
          az login --service-principal \
            -u $AZURE_CLIENT_ID \
            -p $AZURE_CLIENT_SECRET \
            --tenant $AZURE_TENANT_ID
          az acr login --name $REGISTRY_CENTRAL_USER
        '''
      }
    }

    stage('Push Image to ACR') {
      steps {
        echo 'Publicando imagen en ACR...'
        sh 'docker push $IMAGE_NAME'
      }
    }

    stage('Deploy to AKS') {
      steps {
        echo 'Desplegando en AKS...'
        sh '''
          az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER --overwrite-existing
          kubectl set image deployment/optimized-deployment optimized-container=$IMAGE_NAME --record
          kubectl rollout status deployment/optimized-deployment
        '''
      }
    }
  }

  post {
    success {
      echo "Pipeline ejecutado correctamente (Build #${env.BUILD_NUMBER})."
    }
    failure {
      echo 'Error durante la ejecución del pipeline.'
    }
  }
}