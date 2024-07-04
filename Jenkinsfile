pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = 'mydockerhub'
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
        //BACKEND
        stage('Build and Push Backend Docker Image') {
            steps {
                dir('api') {
                    script {
                        def version = sh(script: "jq -r .version package.json", returnStdout: true).trim()
                        withDockerRegistry([credentialsId: DOCKER_HUB_CREDENTIALS, url: 'https://index.docker.io/v1/']) {
                            sh "docker build -t ravisaketi08/backend-app:${version} ."
                            sh "docker push ravisaketi08/backend-app:${version}"
                        }
                        echo 'Successfully Build and Push Backend Docker Image to DockerHub'
                    }
                }
            }
        }
        //FRONTEND
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

        stage('Final Message') {
            steps {
                echo "Successfully built the Docker images and pushed to Docker Hub!"
            }
        }
    }
}
