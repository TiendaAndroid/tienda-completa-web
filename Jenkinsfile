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

        stage('Stop Running Containers') {
            steps {
                dir('tienda-completa-web') {
                    // Detener y eliminar los contenedores actuales
                    sh 'docker-compose down'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('tienda-completa-web/backend-tienda') {
                    // Instalar dependencias
                    sh 'npm install'
                }
            }
        }

        stage('Build Project') {
            steps {
                dir('tienda-completa-web/backend-tienda') {
                    // Construir el proyecto
                    sh 'npm run build'
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

        stage('Run Docker Containers') {
            steps {
                dir('tienda-completa-web') {
                    // Ejecutar contenedores Docker
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
