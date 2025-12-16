pipeline {
    agent any

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
                    sh "docker build -t myflaskapp:latest ."
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    echo "Stopping old container if exists..."
                    sh "docker stop flaskdemo || exit 0"
                    sh "docker rm flaskdemo || exit 0"
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    echo "Running new container on port 5000..."
                    sh "docker run -d --name flaskdemo -p 5000:5000 myflaskapp:latest"
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    echo "Waiting for app to start..."
                    sleep(5)

                    echo "Testing application on port 5000..."
                    sh "curl http://localhost:5000"
                }
            }
        }

        stage('Deploy Success') {
            steps {
                echo "Flask App is running successfully at: http://localhost:5000"
            }
        }
    }
}


