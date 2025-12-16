pipeline {
    agent any

    environment {
        APP_NAME = "flaskdemo"
        IMAGE_NAME = "myflaskapp:latest"
        PORT = "5000"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/RvM07/pipeline'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Flask Docker Image..."
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    echo "Stopping old container if exists..."
                    sh """
                    if docker ps -a --format '{{.Names}}' | grep -q '^${APP_NAME}\$'; then
                        docker stop ${APP_NAME} || true
                        docker rm ${APP_NAME} || true
                    fi
                    """
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    echo "Running new container on port ${PORT}..."
                    sh "docker run -d --name ${APP_NAME} -p ${PORT}:${PORT} ${IMAGE_NAME}"
                }
            }
        }

        stage('Wait for App') {
            steps {
                script {
                    echo "Waiting for Flask app to start..."
                    retry(10) {  // Retry up to 10 times (about 50s total if sleep 5s)
                        sleep 5
                        def status = sh(script: "curl -s -o /dev/null -w '%{http_code}' http://localhost:${PORT}", returnStdout: true).trim()
                        if (status != "200") {
                            error("App not ready yet")
                        }
                    }
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    echo "Testing application on port ${PORT}..."
                    sh "curl http://localhost:${PORT}"
                }
            }
        }

        stage('Deploy Success') {
            steps {
                echo "Flask App is running successfully at: http://localhost:${PORT}"
            }
        }
    }
}
