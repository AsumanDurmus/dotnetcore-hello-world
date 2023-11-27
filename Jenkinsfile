pipeline {
    agent none

    environment {
        APPLICATION = 'dotnetsample'
        DOCKER_REGISTRY = 'hasanalperen'
        IMAGE = '${DOCKER_REGISTRY}/${APPLICATION}:${BUILD_NUMBER}'
    }

    stages {
        stage('Checkout') {
            agent any
            steps {
                git 'https://github.com/alperen-selcuk/dotnetcore-hello-world.git'
            }
        }

        stage('Docker Build and Push') {
            agent {
                docker {
                    image 'docker:dind'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                        sh "docker build -t ${IMAGE} . "
                        sh "docker push ${IMAGE}"
                    }
                }
            }
        }

        stage('Kubernetes Deployment') {
            agent {
                docker {
                    image 'hasanalperen/kubectl'
                    args '-v /root/azkubeconfig:/.kube/config'
                }
            }
            steps {
                script {
                        sh 'kubectl get nodes'
                    }
                }
        }
    }
    post {
        success {
            echo 'Pipeline succeeded! You may want to add more post-success actions here.'
        }
        failure {
            echo 'Pipeline failed! You may want to add more post-failure actions here.'
        }
    }
}
    
