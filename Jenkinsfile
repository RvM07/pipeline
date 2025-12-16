pipeline {
    agent any

    environment {
        DOCKER = "/usr/local/bin/docker"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/RvM07/pipeline'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "${DOCKER} build -t myflaskapp:latest ."
            }
        }

        stage('Stop Old Container') {
            steps {
                sh "${DOCKER} stop flaskdemo || true"
                sh "${DOCKER} rm flaskdemo || true"
            }
        }

        stage('Run New Container') {
            steps {
                sh "${DOCKER} run -d --name flaskdemo -p 5000:5000 myflaskapp:latest"
            }
        }

        stage('Test Application') {
            steps {
                sleep(time: 5, unit: 'SECONDS')
                sh "curl http://localhost:5000"
            }
        }
    }
}

