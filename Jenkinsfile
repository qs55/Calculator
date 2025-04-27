pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "qaiser55/hello-flask-app"
        REMOTE_SERVER = "34.127.71.6"  // IP address of your remote server
        REMOTE_USER = "qshabbir"     // SSH username
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub', url: '') {
                    script {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    // Ensure the SSH Agent Plugin is installed and the credentials are set up correctly
                    sshagent(credentials: ['qs-jumpbox']) {
                        sh """
                            ssh ${REMOTE_USER}@${REMOTE_SERVER} << 'EOF'
                            docker pull ${DOCKER_IMAGE}
                            docker stop my-flask-app || true  # Stop the old container if it exists
                            docker rm my-flask-app || true    # Remove the old container
                            docker run -d --name my-flask-app -p 5000:5000 ${DOCKER_IMAGE}  # Start the new container
                            EOF
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
