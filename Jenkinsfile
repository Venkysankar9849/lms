pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = 'mydockerhub'
        AWS_REGION = 'us-west-1'
        AWS_CREDENTIALS = 'awsid'
        KUBECONFIG = "${env.WORKSPACE}/.kube/config"
    }
    stages {
        stage('Docker Cleaning') {
            steps {
                script {
                    // Stop and remove all Docker containers
                    sh 'docker ps -a -q | xargs --no-run-if-empty docker stop || true'
                    sh 'docker ps -a -q | xargs --no-run-if-empty docker rm || true'
                    // Remove all Docker images and prune Docker system
                    sh 'docker images -a -q | xargs --no-run-if-empty docker rmi || true'
                    sh 'docker system prune -f || true'
                }
                echo 'Cleaning up Docker completed'
            }
        }
        stage('Build and Push Backend Docker Image') {
            steps {
                dir('api') {
                    script {
                        def version = sh(script: "jq -r .version package.json", returnStdout: true).trim()
                        withDockerRegistry([credentialsId: DOCKER_HUB_CREDENTIALS, url: 'https://index.docker.io/v1/']) {
                            sh "docker build -t ravisaketi08/backend-lms:${version} ."
                            sh "docker push ravisaketi08/backend-lms:${version}"
                        }
                        echo 'Successfully Build and Push Backend Docker Image to DockerHub'
                    }
                }
            }
        }
        stage('Deploy Backend to EKS') {
            steps {
                echo 'Configuring EKS Cluster...'
                withAWS(credentials: AWS_CREDENTIALS, region: AWS_REGION) {
                    sh "aws eks update-kubeconfig --name eks-cluster --region $AWS_REGION --kubeconfig $KUBECONFIG"
                    dir('api') {
                        echo 'Deploying backend database...'
                        sh 'kubectl apply -f pg-deployment.yml'
                        sh 'kubectl apply -f pg-service.yml'
                        
                        echo 'Deploying backend...'
                        sh 'kubectl apply -f be-configmap.yml'
                        sh 'kubectl apply -f be-deployment.yml'
                        sh 'kubectl apply -f be-service.yml'
                    }
                    echo 'Successfully deploy backend and database'
                }
            }
        }
        stage('Build and Push Frontend Docker Image') {
            steps {
                dir('webapp') {
                    script {
                        def version = sh(script: "jq -r .version package.json", returnStdout: true).trim()
                        withDockerRegistry([credentialsId: DOCKER_HUB_CREDENTIALS, url: 'https://index.docker.io/v1/']) {
                            sh "docker build -t ravisaketi08/frontend-lms:${version} ."
                            sh "docker push ravisaketi08/frontend-lms:${version}"
                        }
                        echo 'Successfully Build and Push Frontend Docker Image to DockerHub'
                    }
                }
            }
        }
        stage('Deploy Frontend to EKS') {
            steps {
                echo 'Configuring EKS Cluster...'
                withAWS(credentials: AWS_CREDENTIALS, region: AWS_REGION) {
                    sh "aws eks update-kubeconfig --name eks-cluster --region $AWS_REGION --kubeconfig $KUBECONFIG"
                    dir('webapp') {
                        echo 'Deploying Frontend to EKS...'
                        sh 'kubectl apply -f fe-deployment.yml'
                        sh 'kubectl apply -f fe-service.yml'
                    }
                    echo 'Successfully deploy frontend'
                }
            }
        }
        stage('Final Message') {
            steps {
                echo "Successfully completed deploying LMS app!"
            }
        }
    }
}
