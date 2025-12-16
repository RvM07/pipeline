pipeline {
    agent any

    environment {
        PATH = "/bin:/usr/bin:/usr/local/bin:/opt/homebrew/bin:$PATH"
        DOCKER_IMAGE = "myflaskapp:latest"
        CONTAINER_NAME = "flaskdemo"
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
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    echo "Stopping old container if exists..."
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    echo "Running new container on port 5000..."
                    sh "docker run -d --name ${CONTAINER_NAME} -p 5000:5000 ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    echo "Waiting for app to start..."
                    sleep(5)

                    echo "Testing application on port 5000..."
                    sh "curl -f http://localhost:5000 || exit 1"
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
