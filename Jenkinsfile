pipeline {
    environment {
        IMAGEN = "geobas/pin1"
        USUARIO = 'USER_DOCKERHUB'
    }
    agent any
    stages {
        stage('Clone') {
            steps {
                git branch: "main", url: 'https://github.com/geobasti222/jenkins_docker.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    newApp = docker.build("$IMAGEN:$BUILD_NUMBER")
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    docker.image("$IMAGEN:$BUILD_NUMBER").inside('-u root') {
                        // Añadir depuración para ver el estado del contenedor
                        sh 'echo "Testing Apache version inside the container..."'
                        sh 'whoami'
                        sh 'apache2ctl -v || echo "Failed to execute apache2ctl"'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    docker.withRegistry('', USUARIO) {
                        newApp.push()
                    }
                }
            }
        }
        stage('Clean Up') {
            steps {
                sh "docker rmi $IMAGEN:$BUILD_NUMBER"
            }
        }
    }
}
