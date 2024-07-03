pipeline {
    agent any

    environment {
        REGISTRY_CREDENTIALS = credentials('mydockerhub') // Correct credentials ID for Docker Hub
        AWS_CREDENTIALS = 'awsid' // The ID for AWS credentials in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'dev', url: 'https://github.com/Ravisaketi/lms.git'
            }
        }
        
        stage('Set Kubectl Context') {
            steps {
                withAWS(credentials: "${AWS_CREDENTIALS}", region: 'us-west-1') {
                    sh 'aws eks update-kubeconfig --name eks-cluster --region us-west-1'
                }
            }
        }

        stage('Build and Push Backend Docker Images') {
            steps {
                dir('api') {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', "${REGISTRY_CREDENTIALS}") {
                            sh 'docker build -t ravisaketi08/backend-app:latest .'
                            sh 'docker push ravisaketi08/backend-app:latest'
                        }
                    }
                }
            }
        }

        stage('Deploy Backend to EKS') {
            steps {
                script {
                    sh 'kubectl apply -f kubernetes/backend-deployment.yaml'
                }
            }
        }

        stage('Build and Push Frontend Docker Images') {
            steps {
                dir('web') {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', "${REGISTRY_CREDENTIALS}") {
                            sh 'docker build -t ravisaketi08/frontend-app:latest .'
                            sh 'docker push ravisaketi08/frontend-app:latest'
                        }
                    }
                }
            }
        }

        stage('Deploy Frontend to EKS') {
            steps {
                script {
                    sh 'kubectl apply -f kubernetes/frontend-deployment.yaml'
                }
            }
        }

        stage('Clean Up Docker Images') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${REGISTRY_CREDENTIALS}") {
                        sh 'docker rmi ravisaketi08/backend-app:latest || true'
                        sh 'docker rmi ravisaketi08/frontend-app:latest || true'
                    }
                }
            }
        }

        stage('Finish') {
            steps {
                echo 'Pipeline completed successfully!'
            }
        }
    }
}
