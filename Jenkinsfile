pipeline {
    agent any

    environment {
        // Replace with your actual Docker Hub username
        DOCKER_HUB_USER = 'doogadavid' 
        IMAGE_NAME      = 'simple-webapp-flask'
        IMAGE_TAG       = "${env.BUILD_NUMBER}"
	TARGET_IP       = '34.201.30.208'
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
                        inventory: 'localhost,',
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

	stage('Deploy to Production') {
            steps {
                // Using the SSH key you stored in Jenkins to log into the remote server
                ansiblePlaybook(
                    playbook: 'deploy.yml',
                    inventory: "${TARGET_IP},",
                    credentialsId: 'deployment-ssh-key',
                    colorized: true,
                    extraVars: [
                        docker_user: "${DOCKER_HUB_USER}",
                        image_name: "${IMAGE_NAME}",
                        image_tag: "${IMAGE_TAG}"
                    ]
                )
            }
        }
    }
    post {
        success {
            echo "Deployment successful! Visit http://${TARGET_IP}:4000 to see your app."
        }
    }
}


