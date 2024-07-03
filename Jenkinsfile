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
        stage('Final Message') {
            steps {
                echo "You have successfully completed deploying your LMS app!"
            }
        }
    }
}
