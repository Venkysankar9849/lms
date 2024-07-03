pipeline {
    agent none  // Disable node usage
    
    stages {
        stage('Docker Cleaning') {
            agent {
                label 'slave'  // Optional: Specify label if you need a specific node for this stage
            }
            steps {
                script {
                    sh 'docker stop $(docker ps -a -q) || true'
                    sh 'docker rm $(docker ps -a -q) || true'
                    sh 'docker rmi $(docker images -a -q) || true'
                    sh 'docker system prune -f || true'
                }
                echo 'Cleaning up Docker completed'
            }
        }
        
        stage('Build and Push Backend Docker Images') {
            agent {
                label 'slave'  // Optional: Specify label if you need a specific node for this stage
            }
            steps {
                dir('api') {
                    script {
                        withDockerRegistry([credentialsId: 'mydockerhub', url: 'https://index.docker.io/v1/']) {
                            sh 'docker build -t ravisaketi08/backend-app:latest .'
                            sh 'docker push ravisaketi08/backend-app:latest'
                        }
                    }
                }
            }
        }
        
        stage('Deploy Backend to EKS') {
            agent {
                label 'slave'  // Optional: Specify label if you need a specific node for this stage
            }
            steps {
                echo 'Configuring EKS Cluster...'
                sh 'aws eks update-kubeconfig --name eks-cluster --region us-west-1'
                
                dir('api') {
                    echo 'Deploying backend database...'
                    sh 'kubectl apply -f pg-deployment.yaml'
                    sh 'kubectl apply -f pg-service.yaml'
                    
                    echo 'Deploying backend application...'
                    sh 'kubectl apply -f be-configmap.yaml'
                    sh 'kubectl apply -f be-deployment.yaml'
                    sh 'kubectl apply -f be-service.yaml'
                }
            }
        }
        
        stage('Build and Push Frontend Docker Images') {
            agent {
                label 'slave'  // Optional: Specify label if you need a specific node for this stage
            }
            steps {
                dir('webapp') {
                    script {
                        withDockerRegistry([credentialsId: 'mydockerhub', url: 'https://index.docker.io/v1/']) {
                            sh 'docker build -t ravisaketi08/frontend-app:latest .'
                            sh 'docker push ravisaketi08/frontend-app:latest'
                        }
                    }
                }
            }
        }
        
        stage('Deploy Frontend to EKS') {
            agent {
                label 'slave'  // Optional: Specify label if you need a specific node for this stage
            }
            steps {
                echo 'Configuring EKS Cluster...'
                sh 'aws eks update-kubeconfig --name eks-cluster --region us-west-1'
                
                dir('webapp') {
                    echo 'Deploying frontend application...'
                    sh 'kubectl apply -f fe-deployment.yaml'
                    sh 'kubectl apply -f fe-service.yaml'
                }
            }
        }

        stage('Final Message') {
            agent any  // Use 'any' agent to run the final stage on any available node
            steps {
                echo "You have successfully completed deploying your LMS app!"
            }
        }
    }
}
