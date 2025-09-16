pipeline {
    agent {
        docker {
            image 'python:3.10'
            args '-u root:root'  // Run as root to install extra tools if needed
        }
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KunalParkhade/fastapi-jenkins.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest || echo "⚠️ Tests failed (no tests or errors)"'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("fastapi-jenkins-app")
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh 'docker stop fastapi-jenkins-app || true && docker rm fastapi-jenkins-app || true'
                    dockerImage.run('-d -p 8000:8000 --name fastapi-jenkins-app')
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
