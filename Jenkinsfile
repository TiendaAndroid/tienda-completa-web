pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/TiendaAndroid/tienda-completa-web.git'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                // Limpiar el workspace antes de empezar
                deleteDir()
            }
        }

        stage('Clone Repository with Submodules') {
            steps {
                // Clonar el repositorio e inicializar los submódulos
                sh "git clone --recurse-submodules ${env.GIT_REPO}"
                dir('tienda-completa-web') {
                    sh 'git submodule update --init --recursive'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('tienda-completa-web') {
                    // Ejecutar pruebas antes de construir
                    sh 'docker-compose build'
                    sh 'docker-compose run tienda-completa-web npm test' // Ajusta `app_name` al nombre adecuado
                }
            }
        }

        stage('Stop Running Containers') {
            steps {
                dir('tienda-completa-web') {
                    // Detener y eliminar los contenedores actuales
                    sh 'docker-compose down'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                dir('tienda-completa-web') {
                    // Construir imágenes Docker
                    sh 'docker-compose build'
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                dir('tienda-completa-web') {
                    // Subir los contenedores en modo detached
                    sh 'docker-compose up -d'
                }
            }
        }
    }

    post {
        success {
            echo 'Application successfully deployed to production.'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more information.'
        }
    }
}

