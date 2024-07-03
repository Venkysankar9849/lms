pipeline {
    agent any
    
    environment {
        AWS_REGION = 'us-west-1'
        AWS_CREDENTIALS = 'awsid'
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
                script {
                    // Ensure kubectl is configured (already done manually)
                    // kubectl config use-context <your-context> (if needed)
                    
                    // Verify kubectl configuration
                    sh 'kubectl version --client'
                    sh 'kubectl config view'
                    
                    // List pods to verify connectivity
                    sh 'kubectl get pods --all-namespaces'
                    
                    // Deploy your application
                    sh 'kubectl apply -f pg-deployment.yml'
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
