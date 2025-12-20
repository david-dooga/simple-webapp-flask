pipeline {
    agent any
    environment {
        REGISTRY_URL = 'https://registry.hub.docker.com'
        REGISTRY_CREDENTIALS = 'docker-cred'
        IMAGE_NAME = 'doogadavid/myimage'
        IMAGE_TAG = "${env.BUILD_ID}"
    }
    stages {
        stage ('Checkout Source Code') {
            steps {
                checkout scm
            }
        }
        stage ('Build Docker Image') {
            steps {
                script {
                    def customImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                    customImage.tag("latest")
                }
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry("${REGISTRY_URL}", "${REGISTRY_CREDENTIALS}") {
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push()
                        docker.image("${IMAGE_NAME}:latest").push
                    }
                }
            }
        }
    }
    post {
        always {
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
            sh "docker rmi ${IMAGE_NAME}:latest"
        }
    }
}
