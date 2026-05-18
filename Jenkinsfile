pipeline {

    agent any

    environment {
        IMAGE_NAME = "parveenbarak/python-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/barak9/python-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }
stage('Deploy to Kubernetes') {
    steps {

        sh '''
        export KUBECONFIG=/var/jenkins_home/.kube/config

        kubectl config set-cluster minikube --insecure-skip-tls-verify=true

        kubectl set image deployment/python-app \
        python-app=$IMAGE_NAME:$IMAGE_TAG -n dev
        '''
    }
}
    }
}