pipeline {
    agent any
    stages {
        stage('docker cleaning') {
            steps {
                echo "cleaning up docker"
                script {
                    //Stop and remove all running containers
                    sh 'docker stop $(docker ps -a -q) || true'
                    sh 'docker rm $(docker ps -a -q) || true'
                    //Remove all docker images
                    sh 'docker rmi $(docker images -a -q) || true'
                    //Cleaning up any other docker resources
                    sh 'docker system prune -f || true'
                }
                echo 'Cleaning up completed'
            }
        }
        stage('check for backend package.json') {
            steps {
                script {
                    echo 'checking if package.json exists in the directory'
                    dir('api'){
                        sh 'ls -l package.json'
                    }
                }
                
            }
        }
        stage('Build and Push Backend docker images') {
            steps {
                echo "build docker images and push to docker hub"
                dir('api') {
                    script {
                        withDockerRegistry([credentialsId: 'ravisaketi08', url: 'https://index.docker.io/v1/']) {
                            sh "docker build -t ravisaketi08/backend-app ."
                            sh "docker push ravisaketi08/backend-app"
                        }
                    }
                }
            }
        }
        stage('check for frontend package.json') {
            steps {
                script {
                    echo 'checking if package.json exists in the directory'
                    dir('webapp'){
                        sh 'ls -l package.json'
                    }
                }
                
            }
        }
        stage('Build and Push frontend docker images') {
            steps {
                echo "build docker images and push to docker hub"
                dir('webapp') {
                    script {
                        withDockerRegistry([credentialsId: 'ravisaketi08', url: 'https://index.docker.io/v1/']) {
                            sh "docker build -t ravisaketi08/frontend-app ."
                            sh "docker push ravisaketi08/frontend-app"
                        }
                    }
                }
            }
        }
    }
}
