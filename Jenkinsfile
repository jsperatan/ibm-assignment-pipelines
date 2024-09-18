pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('81dc5c35-3b9f-4ac3-af60-8bc11619bd32')
        IMAGE_TAG = 'projectquartz/ibm-assignment:latest'
        CONTAINER_NAME = 'web-app'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/jsperatan/ibm-assignment-web-app', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build . -t $IMAGE_TAG'
            }
        }
        stage('Run') {
            steps {
                sh 'docker run -p 5656:5656 --name $CONTAINER_NAME -d $IMAGE_TAG'
            }
        }
        stage ('Test') {
            steps {
                sh 'sleep 10'
                sh "curl --location 'http://jenkins-docker:5656/users/all'" 
                sh "curl --location 'http://jenkins-docker:5656/users/1'"
            }
        }
        stage ('Publish') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $IMAGE_TAG'
            }
        }
    }
    post {
        always {
            sh 'docker stop $CONTAINER_NAME'
            sh 'docker rm $CONTAINER_NAME'
            sh 'docker image rm $IMAGE_TAG'
            sh 'docker logout'
        }
    }
}