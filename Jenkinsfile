pipeline {
    agent {
        docker {
            image 'docker:dind'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
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
                    image 'your-docker-build-agent'
                }
            }
            steps {
                script {
                    docker.withRegistry('https://your-docker-registry', 'your-docker-credentials-id') {
                        def customImage = docker.build("your-docker-registry/your-image-name:${env.BUILD_NUMBER}")
                        customImage.push()
                    }
                }
            }
        }

        stage('Kubernetes Deployment') {
            agent {
                docker {
                    image 'your-kubernetes-deploy-agent'
                }
            }
            steps {
                script {
                    def kubeConfig = credentials('your-kubeconfig-credentials-id')
                    withKubeConfig([credentialsId: kubeConfig, serverUrl: 'your-kubernetes-api-server-url']) {
                        sh 'kubectl apply -f your-kubernetes-deployment-file.yaml'
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
