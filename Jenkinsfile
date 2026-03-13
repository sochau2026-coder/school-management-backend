pipeline {
    agent any

    environment {
        IMAGE_NAME = "sochaucoder/school-backend"
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "school-backend"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/sochau2026-coder/school-management-backend.git'
            }
        }

        stage('Build Jar') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy Backend') {
            steps {
                sh '''
                docker pull $IMAGE_NAME:$IMAGE_TAG
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME -p 8082:8080 $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}