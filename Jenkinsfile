pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-jenkins-app"
        DOCKER_TAG = "latest"
        DOCKER_REPO = "rohith1305/my-jenkins-app"
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/KyathamRohith/jenkins-docker.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }
        stage('Run Container Locally') {
            steps {
                script {
                    sh "docker run -d -p 8080:80 --name my-container ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', '93c470a0-e8fe-425c-8f55-932aae8919d4') {
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REPO}:${DOCKER_TAG}"
                        sh "docker push ${DOCKER_REPO}:${DOCKER_TAG}"
                    }
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                script {
                    sh "ssh user@remote-server 'docker pull ${DOCKER_REPO}:${DOCKER_TAG} && docker run -d -p 80:80 ${DOCKER_REPO}:${DOCKER_TAG}'"
                }
            }
        }
    }
}
