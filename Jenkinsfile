pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker { image 'maven:3.8.8-openjdk-17' }
            }
            steps {
                dir('demo-java-app') {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Test') {
            agent {
                docker { image 'maven:3.8.8-openjdk-17' }
            }
            steps {
                dir('demo-java-app') {
                    sh 'mvn test'
                }
            }
        }
        // Ajoute les autres stages ici
    }
}