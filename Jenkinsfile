pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "akshaychhallare/notes_app"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'DockerHub-cred',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    export KUBECONFIG=/home/ubuntu/.kube/config
                    kubectl apply -f k8s/
                    kubectl set image deployment/notes-app notes-app=$DOCKER_IMAGE:$BUILD_NUMBER
                '''
            }
        }
    }
}

