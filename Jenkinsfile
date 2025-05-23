pipeline {
    agent {
        docker {
            image 'maven:3.8.6-openjdk-11'
            args '-v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker --group-add $(getent group docker | cut -d: -f3)'
        }
    }
    
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
            post {
                success {
                    echo 'Build completed successfully!'
                }
                failure {
                    echo 'Build failed!'
                }
            }
        }
        
        stage('Test') {
            steps {
                dir('demo-java-app') {
                    sh 'mvn test'
                }
            }
            post {
                always {
                    dir('demo-java-app') {
                        // Publier les résultats des tests si disponibles
                        publishTestResults testResultsPattern: 'target/surefire-reports/*.xml', 
                                          allowEmptyResults: true
                    }
                }
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDENTIALS}") {
                        // Construction de l'image Docker
                        def customImage = docker.build("${DOCKER_IMAGE}", "demo-java-app")
                        
                        // Push de l'image
                        customImage.push()
                        customImage.push("latest")
                    }
                }
            }
            post {
                success {
                    echo "Docker image ${DOCKER_IMAGE} pushed successfully!"
                }
                failure {
                    echo 'Docker build/push failed!'
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS}", variable: 'KUBECONFIG')]) {
                    sh '''
                        # Vérifier que kubectl est disponible
                        which kubectl || (echo "kubectl not found" && exit 1)
                        
                        # Mettre à jour l'image dans le deployment
                        sed -i "s|image:.*|image: ${DOCKER_IMAGE}|" k8s/deployment.yaml
                        
                        # Appliquer le deployment
                        kubectl --kubeconfig=$KUBECONFIG apply -f k8s/deployment.yaml
                        
                        # Vérifier le statut du deployment
                        kubectl --kubeconfig=$KUBECONFIG rollout status deployment/demo-java-app --timeout=300s
                    '''
                }
            }
            post {
                success {
                    echo 'Deployment to Kubernetes completed successfully!'
                }
                failure {
                    echo 'Kubernetes deployment failed!'
                }
            }
        }
    }
    
    post {
        always {
            // Nettoyer les images Docker locales pour économiser l'espace
            sh 'docker image prune -f || true'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}