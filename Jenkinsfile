pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-jenkins-app"
        DOCKER_TAG = "latest"
        DOCKER_REPO = "rohith1305/my-jenkins-app"
        DOCKER_CREDENTIALS_ID = "93c470a0-e8fe-425c-8f55-932aae8919d4" // Jenkins credentials ID
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/KyathamRohith/jenkins-docker.git'
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        echo "Logged into Docker Hub"
                    }
                }
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
            sh '''
            docker ps -a -q --filter "name=my-container" | grep -q . && docker stop my-container && docker rm my-container || true
            docker run -d -p 8081:80 --name my-container ${DOCKER_IMAGE}:${DOCKER_TAG}
            '''
        }
    }
}

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REPO}:${DOCKER_TAG}"
                        sh "docker push ${DOCKER_REPO}:${DOCKER_TAG}"
                    }
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                script {
                    sh "ssh master@remote-server 'docker pull ${DOCKER_REPO}:${DOCKER_TAG} && docker run -d -p 80:80 ${DOCKER_REPO}:${DOCKER_TAG}'"
                }
            }
        }
    }
}
