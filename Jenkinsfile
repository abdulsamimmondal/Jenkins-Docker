pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-jenkins-app"
        DOCKER_TAG = "latest"
        DOCKER_REPO = "samimmondal/my-jenkins-app"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials" // Jenkins credentials ID
        CONTAINER_NAME = "mycontainer11"
        CONTAINER_NAME1 = "mycontainer12"

    }
    stages {
        stage('Clone Repository') {
            steps {
                git url:'https://github.com/abdulsamimmondal/Jenkins-Docker.git',branch:'main'
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
                    sh """
                        # Stop and remove existing container if running
                        docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker stop || true
                        docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker rm || true

                        # Run new container
                        docker run -d -p 8095:80 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
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
            // Ensure the SSH key is used for the SSH connection
            withCredentials([sshUserPrivateKey(credentialsId: '00e98742-3c7a-4590-be50-a27521d2bf0b', keyFileVariable: 'SSH_KEY')]) {
                sh """
                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY master@192.168.203.128 '
                    docker pull samimmondal/my-jenkins-app:latest &&
                    docker ps -a -q --filter name=mycontainer12 | xargs -r docker stop || true &&
                    docker ps -a -q --filter name=mycontainer12 | xargs -r docker rm || true &&
                    docker run -d -p 80:80 --name mycontainer12 samimmondal/my-jenkins-app:latest'
                """
            }
        }
    }
}

