pipeline {
  agent any
  environment {
    DOCKER_IMAGE = "hajarek24/demo-java-app:${BUILD_NUMBER}"
    DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'
    KUBECONFIG_CREDENTIALS = 'kubeconfig'
  }
  stages {
    stage('Build') {
      steps {
        dir('demo-java-app') {
          sh 'mvn clean package'
        }
      }
    }
    stage('Test') {
      steps {
        dir('demo-java-app') {
          sh 'mvn test'
        }
      }
    }
    stage('Docker Build & Push') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDENTIALS}") {
            sh "docker build -t ${DOCKER_IMAGE} demo-java-app"
            sh "docker push ${DOCKER_IMAGE}"
          }
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS}", variable: 'KUBECONFIG')]) {
          sh '''
            sed -i "s|image:.*|image: ${DOCKER_IMAGE}|" k8s/deployment.yaml
            kubectl --kubeconfig=$KUBECONFIG apply -f k8s/deployment.yaml
          '''
        }
      }
    }
  }
}