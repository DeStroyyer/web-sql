pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials-id' // Укажите ID ваших Docker Hub credentials
        DOCKER_IMAGE = "yourdockerhubusername/nodejs-app" // Укажите ваш Docker Hub username и имя образа
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/yourusername/nodejs-app.git' // Укажите ваш репозиторий
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    def app = docker.image(DOCKER_IMAGE)
                    app.inside {
                        sh 'npm install'
                        sh 'npm test'
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }
    }

    post {
        failure {
            echo "Tests failed"
        }
    }
}
