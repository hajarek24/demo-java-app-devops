pipeline {
    agent any
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
        // autres stages...
    }
}