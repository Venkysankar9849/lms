pipeline {
    agent any

    stages {
        stage('Docker Cleaning') {
            steps {
                script {
                    def containers = sh(script: 'docker ps -a -q', returnStdout: true).trim()
                    if (containers) {
                        sh "docker stop ${containers}"
                        sh "docker rm ${containers}"
                    }
                    def images = sh(script: 'docker images -a -q', returnStdout: true).trim()
                    if (images) {
                        sh "docker rmi ${images}"
                    }
                    sh 'docker system prune -f'
                }
                echo 'Cleaning up Docker completed'
            }
        }
        
        stage('Build and Push Backend Docker Images') {
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
            steps {
                echo 'Configuring EKS Cluster...'
                sh 'aws eks update-kubeconfig --name eks-cluster --region us-west-1'
                
                dir('api') {
                    echo 'Deploying backend database...'
                    sh 'kubectl apply -f pg-deployment.yml'
                    sh 'kubectl apply -f pg-service.yml'
                    
                    echo 'Deploying backend application...'
                    sh 'kubectl apply -f be-configmap.yml'
                    sh 'kubectl apply -f be-deployment.yml'
                    sh 'kubectl apply -f be-service.yaml'
                }
            }
        }                
        stage('Build and Push Frontend Docker Images') {
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
        stage('Final Message') {
            steps {
                echo "You have successfully completed deploying your LMS app!"
            }
        }
    }
}
