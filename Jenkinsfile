pipeline {
    agent any
    environment {
        PROJECT_ID = 'docker'
        CLUSTER_NAME = 'jenkins'
        LOCATION = 'us-central-1a'
        CREDENTIALS_ID = 'kubernetes'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build("ransuum/pipeline:${env.BUILD_ID}")
                }
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        app.push("${env.BUILD_ID}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f deployment.yaml"
            }
        }
    }
}
