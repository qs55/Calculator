pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "qaiser55/calculator"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/qs55/Calculator.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Test') {
            steps {
                sh 'pip install -r requirements.txt'
                sh 'pytest test/'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub', url: '') {
                    sh "docker push ${DOCKER_IMAGE}"
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
