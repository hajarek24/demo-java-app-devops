pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "hajarek24/demo-java-app:${BUILD_NUMBER}"
    SONAR_URL = "http://sonarqube:9000"
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
    stage('Static Code Analysis') {
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
          dir('demo-java-app') {
            sh 'mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
          }
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