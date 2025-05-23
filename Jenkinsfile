pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                dir('demo-java-app') { // <-- add this line
                    sh 'mvn clean package'
                }
            }
        }
        stage('Test') {
            steps {
                dir('demo-java-app') { // <-- add this line
                    sh 'mvn test'
                }
            }
        }
    }
}