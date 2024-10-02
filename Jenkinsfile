pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/TiendaAndroid/tienda-completa-web.git'
        GIT_CREDENTIALS_ID = 'Git-hub_credencials' // Cambia esto por el ID de tus credenciales configuradas en Jenkins
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
                // Clonar el repositorio e inicializar los submódulos con credenciales
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: env.GIT_REPO, credentialsId: env.GIT_CREDENTIALS_ID]],
                    extensions: [[$class: 'SubmoduleOption', recursiveSubmodules: true, trackingSubmodules: false]]
                ])
            }
        }

        stage('Run Tests') {
            steps {
                dir('tienda-completa-web') {
                    // Ejecutar pruebas antes de construir
                    sh 'docker-compose build'
                    sh 'docker-compose run app_name npm test' // Ajusta `app_name` al nombre adecuado
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
