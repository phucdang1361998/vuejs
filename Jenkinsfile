pipeline {
    agent any

    environment {
        HARBOR_URL = "harbor.learnserver.online"
        HARBOR_PROJECT = "vuejs"
        IMAGE_NAME = "vuejs"
        IMAGE_TAG = "latest"  
        DOCKER_CREDENTIALS_ID = "harbor"    
    }

    stages {
        // stage('Setup enviroment') {
        //     steps {
        //        script {
        //         //  sh """apt-get update -y"""
        //         //  sh """apt-get install -y npm"""
        //         //  sh """apt-get install -y docker.io"""
        //          sh """npm i"""
        //        }
        //     }
        // }

        // stage('Build repository') {
        //     steps {
        //         script {
        //             sh """npm run build"""
        //         }
        //     }
        // }

        stage('Build Images') {
            steps {
                script {
                    sh """docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."""
                    sh """docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${HARBOR_URL}/${HARBOR_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG}"""
                }
            }
        }

        stage('Push Images to registry') {
            steps {
                script {
                       withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                            echo $DOCKER_PASSWORD | docker login ${HARBOR_URL} -u $DOCKER_USER --password-stdin
                            docker push ${HARBOR_URL}/${HARBOR_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Pull Images') {
            steps {
                script {
                        sh """
                            docker pull ${HARBOR_URL}/${HARBOR_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG}
                        """
                }
            }
        }

        stage('Deploy images and remove old images') {
            steps {
                script {
                        sh """
                                docker stop ${IMAGE_NAME} || true
                                docker rm ${IMAGE_NAME} || true
                        """
                        sh """
                                docker run -d --name ${IMAGE_NAME} ${HARBOR_URL}/${HARBOR_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG}
                        """
                }
            }
        }
    }
}
