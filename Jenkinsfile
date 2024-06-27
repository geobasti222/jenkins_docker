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
        stage('Scan Image') {
            steps {
                script {
                    // Ejemplo usando Trivy para escanear la imagen
                    sh "trivy --exit-code 0 --severity HIGH --no-progress $IMAGEN:$BUILD_NUMBER"
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
