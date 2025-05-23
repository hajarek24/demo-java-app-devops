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
                    echo 'Test stage completed'
                }
                success {
                    echo 'Tests passed successfully!'
                }
                failure {
                    echo 'Tests failed!'
                }
            }
        }
        
        stage('Docker Build & Push') {
            when {
                // Seulement si Docker est disponible
                expression { 
                    return sh(script: 'which docker', returnStatus: true) == 0
                }
            }
            steps {
                script {
                    try {
                        // Vérifier si Docker fonctionne
                        sh 'docker --version'
                        
                        // Construction de l'image Docker
                        sh "docker build -t ${DOCKER_IMAGE} demo-java-app"
                        
                        // Push vers Docker Hub (si les credentials sont configurés)
                        withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", 
                                                        usernameVariable: 'DOCKER_USER', 
                                                        passwordVariable: 'DOCKER_PASS')]) {
                            sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                            sh "docker push ${DOCKER_IMAGE}"
                            sh "docker tag ${DOCKER_IMAGE} hajarek24/demo-java-app:latest"
                            sh "docker push hajarek24/demo-java-app:latest"
                        }
                    } catch (Exception e) {
                        echo "Docker not available or credentials not configured: ${e.getMessage()}"
                        currentBuild.result = 'UNSTABLE'
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
            when {
                // Seulement si kubectl est disponible
                expression { 
                    return sh(script: 'which kubectl', returnStatus: true) == 0
                }
            }
            steps {
                script {
                    try {
                        withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS}", variable: 'KUBECONFIG')]) {
                            sh '''
                                # Vérifier que kubectl fonctionne
                                kubectl --kubeconfig=$KUBECONFIG version --client
                                
                                # Mettre à jour l'image dans le deployment
                                sed -i "s|image:.*|image: ${DOCKER_IMAGE}|" k8s/deployment.yaml
                                
                                # Appliquer le deployment
                                kubectl --kubeconfig=$KUBECONFIG apply -f k8s/deployment.yaml
                                
                                # Vérifier le statut du deployment
                                kubectl --kubeconfig=$KUBECONFIG rollout status deployment/demo-java-app --timeout=300s
                            '''
                        }
                    } catch (Exception e) {
                        echo "Kubernetes deployment not available or credentials not configured: ${e.getMessage()}"
                        currentBuild.result = 'UNSTABLE'
                    }
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
            node {
                echo 'Pipeline completed'
                // Nettoyer les images Docker si disponible
                script {
                    try {
                        sh 'docker image prune -f || true'
                    } catch (Exception e) {
                        echo "Docker cleanup skipped: ${e.getMessage()}"
                    }
                }
            }
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        unstable {
            echo 'Pipeline completed with warnings (some optional stages failed)'
        }
    }
}