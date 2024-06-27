pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'geobas'
        IMAGE_NAME = 'pruebapin'
        TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                // Clona tu repositorio
                git branch: "main", url: 'https://github.com/geobasti222/jenkins_docker.git'

            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:${TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-registry-credentials', url: "https://${DOCKER_REGISTRY}") {
                        docker.image("${DOCKER_REGISTRY}/${IMAGE_NAME}:${TAG}").push()
                    }
                }
            }
        }

        stage('Scan Docker Image') {
            steps {
                script {
                    sh """
                    docker pull aquasec/trivy:latest
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock -v $WORKSPACE:/root/.cache/ aquasec/trivy image ${DOCKER_REGISTRY}/${IMAGE_NAME}:${TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            // Limpiar las imágenes de Docker
            script {
                sh "docker rmi ${DOCKER_REGISTRY}/${IMAGE_NAME}:${TAG}"
            }
        }
        success {
            echo 'El pipeline ha terminado con éxito.'
        }
        failure {
            echo 'El pipeline ha fallado.'
        }
    }
}

