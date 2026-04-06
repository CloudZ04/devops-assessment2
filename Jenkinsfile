pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "cloudz04/devops-assessment2"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . venv/bin/activate
                pytest || exit 1
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE:build$BUILD_NUMBER .
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                docker tag $DOCKER_IMAGE:build$BUILD_NUMBER $DOCKER_IMAGE:v1.0.$BUILD_NUMBER
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE:v1.0.$BUILD_NUMBER
                    '''
                }
            }
        }
    }
}