pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:${env.PATH}"
        DOCKER_IMAGE = "myflaskapp:latest"
        CONTAINER_NAME = "flaskdemo"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/RvM07/pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Stop Old Container') {
            steps {
                echo "Stopping old container if exists..."
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
            }
        }

        stage('Run New Container') {
            steps {
                echo "Running new container..."
                sh "docker run -d --name ${CONTAINER_NAME} -p 5000:5000 ${DOCKER_IMAGE}"
            }
        }

        stage('Test Application') {
            steps {
                echo "Waiting for container to start..."
                sleep(time: 5, unit: 'SECONDS')
                echo "Testing Flask app..."
                sh "curl -f http://localhost:5000 || exit 1"
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! Flask app is running."
        }
        failure {
            echo "Pipeline failed. Check logs above for details."
        }
        always {
            echo "Pipeline finished."
        }
    }
}

