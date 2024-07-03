pipeline {
    agent any

    stages {
        stage('Docker Cleaning') {
            steps {
                script {
                    def containers = sh(script: 'docker ps -a -q', returnStdout: true).trim()
                    if (containers) {
                        sh "docker stop ${containers}"
                        sh "docker rm ${containers}"
                    }
                    def images = sh(script: 'docker images -a -q', returnStdout: true).trim()
                    if (images) {
                        sh "docker rmi ${images}"
                    }
                    sh 'docker system prune -f'
                }
                echo 'Cleaning up Docker completed'
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
        stage('Deploying App to Kubernetes') {
            steps {
                dir('api') {
                    withCredentials([string(credentialsId: 'secretid', variable: 'KUBECONFIG_CONTENT')]) {
                        script {
                            def kubeconfigFile = "${env.WORKSPACE}/kubeconfig.yaml"
                            writeFile file: kubeconfigFile, text: env.KUBECONFIG_CONTENT
                            kubernetesDeploy(
                                configs: "pg-deployment.yml", 
                                kubeconfigPath: kubeconfigFile
                            )
                        }
                    }
                }
            }
        }                 
        stage('Build and Push Frontend Docker Images') {
            steps {
                dir('webapp') {
                    script {
                        withDockerRegistry([credentialsId: 'mydockerhub', url: 'https://index.docker.io/v1/']) {
                            sh 'docker build -t ravisaketi08/frontend-app:latest .'
                            sh 'docker push ravisaketi08/frontend-app:latest'
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
