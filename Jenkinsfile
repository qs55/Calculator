pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "qaiser55/hello-flask-app"
    }

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
    }

    post {
        always {
            cleanWs()
        }
    }
}
