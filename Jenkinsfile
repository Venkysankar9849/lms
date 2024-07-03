pipeline {
    agent any
    
    environment {
        AWS_REGION = 'us-west-1'
        AWS_CREDENTIALS = 'awsid'
    }
    
    stages {
        stage('Set Kubectl Context') {
            steps {
                sh 'kubectl config use-context arn:aws:eks:us-west-1:211125493013:cluster/eks-cluster'
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
                script {
                    sh 'kubectl get nodes'
                    // Add more Kubernetes deployment steps as needed
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
