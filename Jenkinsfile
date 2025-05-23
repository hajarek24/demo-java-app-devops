pipeline {
  agent any
  parameters {
    string(name: 'build_version', defaultValue: 'V1.0', description: 'Build version to use for Docker image')
  }
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/hajarek24/demo-java-app-devops.git'
      }
    }
    stage('Build and Test') {
      steps {
        sh 'mvn clean package'
      }
    }
    // Add other stages as needed
  }
}