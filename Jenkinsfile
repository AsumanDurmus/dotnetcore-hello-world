pipeline {
    agent none

    environment {
        APPLICATION = 'dotnetsample'
        DOCKER_REGISTRY = 'hasanalperen'
        IMAGE = '${DOCKER_REGISTRY}/${APPLICATION}:${BUILD_NUMBER}'
        KUBE_CONFIG_CREDENTIALS_ID = 'kubeconfig'
    }

    stages {
        stage('Checkout') {
            agent any
            steps {
                checkout scm
            }
        }

        stage('Docker Build and Push') {
            agent {
                docker {
                    image 'docker:dind'
                }
            }
            steps {
                script {
                    {
                    withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                        sh "docker build -t ${IMAGE} . "
                        sh "docker push ${IMAGE}"
                    }
                }
            }
        }
        }
        stage('Kubernetes Deployment') {
            agent {
                docker {
                    image 'd3fk/kubectl:latest'
                }
            }
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeConfig', variable: 'kubeConfig')]) {
                        sh "echo ${kubeConfig} > .kube/config"
                        sh "kubectl get nodes"
                    }
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
    
