pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "qaiser55/hello-flask-app"
        CORTEX_API_URL = "https://api-cortex-cas.io"  // Define the API URL
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

        stage('Cortex Scan') {
            steps {
                withCredentials([string(credentialsId: 'CORTEX_API_ID', variable: 'cortex_api_id'),
                                 string(credentialsId: 'CORTEX_API_KEY', variable: 'cortex_api_key')]) {
                    script {
                        docker.image('cortex/newCLI:latest').inside("--entrypoint=''") {
                            unstash 'source'
                            try {
                                // Run the Cortex scan
                                sh """
                                    newCLI scan . -o cli=console,junitxml=results.xml --api-key ${cortex_api_id}::${cortex_api_key} --repo-id org/repo --branch main
                                """
                                // Publish the results of the scan
                                junit skipPublishingChecks: true, testResults: 'results.xml'
                            } catch (err) {
                                // Ensure the results are published even if the scan fails
                                junit skipPublishingChecks: true, testResults: 'results.xml'
                                throw err
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy to Local Docker') {
            steps {
                script {
                    // Pull the latest image from Docker Hub
                    sh "docker pull ${DOCKER_IMAGE}"

                    // Stop and remove any existing container with the same name (optional)
                    sh "docker stop my-flask-app || true"
                    sh "docker rm my-flask-app || true"

                    // Run the Docker container with the new image
                    sh "docker run -d --name my-flask-app -p 5000:5000 ${DOCKER_IMAGE}"
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
