pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'geobas'
        IMAGE_NAME = 'pruebapin'
        TAG = 'latest'
        USERNAME='geobas'
        PASSWORD='Geoyana1301'
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
            withCredentials([usernamePassword(credentialsId: 'docker-registry-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                def dockerRegistryUrl = "https://${DOCKER_REGISTRY}"
                def dockerLoginCmd = "docker login -u ${USERNAME} -p ${PASSWORD} ${dockerRegistryUrl}"

                // Use --password-stdin for improved security
                sh "echo ${PASSWORD} | ${dockerLoginCmd} --password-stdin"

                // Now you can push your Docker image
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

