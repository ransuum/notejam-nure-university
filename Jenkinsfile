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
					def app = docker.build("ransuum/pipeline:${env.BUILD_ID}")
                    env.APP_IMAGE = app.id
                }
            }
        }
        stage('Push Image to DockerHub') {
			steps {
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
						docker.image(env.APP_IMAGE).push("${env.BUILD_ID}")
                        docker.image(env.APP_IMAGE).push("latest")
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
			steps {
				withKubeConfig([credentialsId: 'kubernetes']) {
					bat "kubectl apply -f deployment.yaml"
				}
            }
        }
    }
}