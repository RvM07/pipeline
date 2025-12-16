pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/shaanman23/my-flask-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Flask Docker Image..."
                sh "docker build -t myflaskapp:latest ."
            }
        }

        stage('Stop Old Container') {
            steps {
                echo "Stopping old container if exists..."
                sh "docker stop flaskdemo || true"
                sh "docker rm flaskdemo || true"
            }
        }

        stage('Run New Container') {
            steps {
                echo "Running new container on port 5000..."
                sh "docker run -d --name flaskdemo -p 5000:5000 myflaskapp:latest"
            }
        }

        stage('Test Application') {
            steps {
                echo "Waiting for app to start..."
                sleep(time: 5, unit: 'SECONDS')

                echo "Testing application on port 5000..."
                sh "curl http://localhost:5000"
            }
        }

        stage('Deploy Success') {
            steps {
                echo "Flask App is running successfully at: http://localhost:5000"
            }
        }
    }
}
