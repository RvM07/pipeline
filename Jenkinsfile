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
        
        stage('Clean Port and Stop Old Container') {
            steps {
                script {
                    echo "Cleaning port ${PORT} and stopping old container..."
                    sh """
                        # Stop and remove old container if exists
                        if docker ps -a --format '{{.Names}}' | grep -q '^${APP_NAME}\$'; then
                            docker stop ${APP_NAME} || true
                            docker rm ${APP_NAME} || true
                        fi
                        
                        # Wait for container to fully stop
                        sleep 2
                        
                        # Kill any process using port ${PORT} (handles macOS AirPlay and stuck processes)
                        lsof -ti:${PORT} | xargs kill -9 2>/dev/null || true
                        
                        # Wait for port to be released
                        sleep 2
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
                    timeout(time: 60, unit: 'SECONDS') {
                        waitUntil {
                            script {
                                def status = sh(
                                    script: "curl -s -o /dev/null -w '%{http_code}' http://localhost:${PORT}",
                                    returnStatus: true
                                )
                                return status == 0
                            }
                        }
                    }
                    echo "Flask app is ready!"
                }
            }
        }
        
        stage('Test Application') {
            steps {
                script {
                    echo "Testing application on port ${PORT}..."
                    sh "curl -f http://localhost:${PORT}"
                }
            }
        }
        
        stage('Deploy Success') {
            steps {
                echo "✅ Flask App is running successfully!"
                echo "🔗 Access it at: http://localhost:${PORT}"
                script {
                    sh "docker ps | grep ${APP_NAME}"
                }
            }
        }
    }
    
    post {
        failure {
            echo "❌ Pipeline failed! Cleaning up..."
            script {
                sh """
                    docker stop ${APP_NAME} 2>/dev/null || true
                    docker rm ${APP_NAME} 2>/dev/null || true
                """
            }
        }
        always {
            echo "Pipeline execution completed."
        }
    }
}
