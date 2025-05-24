pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "hajarek24/demo-java-app:${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/hajarek24/demo-java-app-devops.git'
      }
    }
    stage('Build & Test') {
      steps {
        dir('demo-java-app') {
          sh 'mvn clean package'
          sh 'mvn test'
        }
      }
    }
    stage('Build & Push Docker Image') {
      environment {
        REGISTRY_CREDENTIALS = credentials('dockerhub-credentials')
      }
      steps {
        script {
          sh 'docker build -t ${DOCKER_IMAGE} demo-java-app'
          docker.withRegistry('https://index.docker.io/v1/', "dockerhub-credentials") {
            sh "docker push ${DOCKER_IMAGE}"
          }
        }
      }
    }
    // Déploiement Kubernetes (optionnel)
    stage('Deploy to Kubernetes') {
      when { expression { return fileExists('k8s/deployment.yaml') } }
      steps {
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
          sh '''
            sed -i "s|image:.*|image: ${DOCKER_IMAGE}|" k8s/deployment.yaml
            kubectl --kubeconfig=$KUBECONFIG apply -f k8s/deployment.yaml
          '''
        }
      }
    }
  }
}