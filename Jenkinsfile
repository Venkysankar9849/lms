pipeline {
    agent any
    
    environment {
        AWS_REGION = 'us-west-1'
        AWS_CREDENTIALS = 'awsid' // Ensure this matches your Jenkins credentials ID
    }
    
    stages {
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
                script {
                    withAWS(credentials: 'awsid', region: 'us-west-1') {
                        dir('api') {
                            // Example: Deploy backend using kubectl apply
                            sh 'kubectl apply -f pg-deployment.yml'
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
