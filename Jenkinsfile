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
                        docker.withRegistry('https://hub.docker.com', '4cbeff25-abc9-43cf-9165-12be0da557a4') {
                        // Docker build and push commands
                        docker.build('backend-app:latest').push()
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
                        docker.withRegistry('https://hub.docker.com', '4cbeff25-abc9-43cf-9165-12be0da557a4') {
                        // Docker build and push commands
                        docker.build('frontend-app:latest').push()
                        }
                    }
                }
            }
        }
    }
}
