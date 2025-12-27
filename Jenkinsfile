pipeline {
    agent any

    environment {
        // Replace with your actual Docker Hub username
        DOCKER_HUB_USER = 'daviddooga' 
        IMAGE_NAME      = 'simple-webapp-flask'
        IMAGE_TAG       = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Source') {
            steps {
                // Pulls your latest code from GitHub
                git branch: 'master', 
                    url: 'https://github.com/david-dooga/simple-webapp-flask.git'
            }
        }

        stage('Ansible Build & Push') {
            steps {
                // Securely fetches credentials from Jenkins and masks them in logs
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', 
                                                 usernameVariable: 'DOCKER_USER', 
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    
                    // Executes the Ansible playbook with variables passed from Jenkins
                    ansiblePlaybook(
                        playbook: 'docker-build-push.yml',
                        inventory: 'web,',
                        extraVars: [
                            docker_user: "${DOCKER_HUB_USER}",
                            docker_password: "${DOCKER_PASS}",
                            image_name: "${IMAGE_NAME}",
                            image_tag: "${IMAGE_TAG}",
                            workspace_path: "${env.WORKSPACE}"
                        ]
                    )
                }
            }
        }
    }
}

