pipeline {
    agent any
    
    environment {
        AWS_REGION = 'us-west-1'
        AWS_CREDENTIALS = 'awsid' // This is the ID of your AWS credentials in Jenkins
    }
    
    stages {
        stage('Docker Cleaning') {
            steps {
                script {
                    // Stop and remove all Docker containers
                    sh 'docker stop $(docker ps -a -q) || true'
                    sh 'docker rm $(docker ps -a -q) || true'
                    
                    // Remove all Docker images and prune Docker system
                    sh 'docker rmi $(docker images -a -q) || true'
                    sh 'docker system prune -f || true'
                }
                echo 'Cleaning up Docker completed'
            }
        }
        
        stage('Build and Push Backend Docker Images') {
            steps {
                dir('api') {
                    script {
                        withDockerRegistry([credentialsId: 'mydockerhub', url: 'https://index.docker.io/v1/']) {
                            // Build backend Docker image
                            sh 'docker build -t ravisaketi08/backend-app:latest .'
                            
                            // Push backend Docker image to Docker Hub
                            sh 'docker push ravisaketi08/backend-app:latest'
                        }
                    }
                }
            }
        }
        
        stage('Deploy Backend to EKS') {
            steps {
                echo 'Configuring EKS Cluster...'
                withAWS(credentials: 'awsid', region: 'us-west-1') {
                    dir('api') {
                        // Deploy backend database
                        sh 'kubectl apply -f pg-deployment.yml'
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
