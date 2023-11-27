pipeline {
    agent none

    environment {
        APPLICATION = 'dotnetsample'
        DOCKER_REGISTRY = 'hasanalperen'
        IMAGE = '${DOCKER_REGISTRY}/${APPLICATION}:${BUILD_NUMBER}'
        GIT_REPO = "argo-deployment"
        GIT_USER = "alperen-selcuk"
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

// TRADIONAL DEPLOYMENT
//        stage('Kubernetes Deployment') {
//            agent {
//                docker {
//                    image 'hasanalperen/kubectl'
//                    args '-v /root/azkubeconfig:/.kube/config'
//                }
//            }
//            steps {
//                script {
//                        sh 'kubectl apply -f deployment.yaml'
//                    }
//                }
//        }

// ARGO DEPLOYMENT
        stage('Update Deployment File') {
            agent {
                docker {
                    image 'mrnonz/alpine-git-curl'
                }
            }
            steps {
                    git (
                        url: "https://github.com/${GIT_USER}/${GIT_REPO}.git",
                        branch: "main",
                        poll: true
                    )
                    sh '''
                        ls
                        git config user.email "alperenhasanselcuk@gmail.com"
                        git config user.name "Alperen SELCUK"
                        BUILD_NUMBER=${BUILD_NUMBER}
                        sed -i 's|image: ${DOCKER_REGISTRY}/${APPLICATION}:[0-9]*|image: ${DOCKER_REGISTRY}/${APPLICATION}:${BUILD_NUMBER}|' deployment.yaml
                        git status
                        git add deployment.yaml
                        git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                        '''
                        withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                            sh "git push origin main"
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
    
