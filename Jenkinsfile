pipeline {
    agent any
    
    environment {
        AWS_REGION = 'us-west-1'
        AWS_CREDENTIALS = 'awsid' // This is the ID of your AWS credentials in Jenkins
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
                dir('./api') {
                    script {
                        withAWS(credentials: 'awsid', region: 'us-west-1') {
                            sh 'kubectl version --client' // Verify kubectl version
                            sh 'kubectl config view' // Display kubectl config for debugging
                            sh 'kubectl get pods --all-namespaces' // List pods to verify connectivity
                            sh 'kubectl apply -f pg-deployment.yml' // Attempt deployment
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
