pipeline {
    environment {
        REGISTRY_CREDENTIAL = '04ccec49-0d7c-47f7-a031-36b221383033'
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKERHUB_USERNAME = 'dekelly'
        APP_NAME = 'python-app'
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + '/' + "${APP_NAME}"
    }
    agent any
    stages {
        stage('Checkout Source') {
            steps {
                git 'https://github.com/DeanKelly751/final-pipeline.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build IMAGE_NAME
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', REGISTRY_CREDENTIAL) {
                        dockerImage.push("${BUILD_NUMBER}")
                    }
                }
            }
        }
        stage('Modify the image in Kubernetes deployment file') {
            steps {
                sh 'cat deployment.yaml'
                sh "sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml"
                sh 'cat deployment.yaml'
            }
        }
        stage('Push the changed deployment file to Git') {
            steps {
                script {
                    sh """
                    git config --global user.name "DeanKelly751"
                    git config --global user.email "deanok1000@gmail.com"
                    git add deployment.yaml
                    git commit -m 'Updated the deployment file: ${BUILD_NUMBER}' """
                    withCredentials([usernamePassword(credentialsId: '316e0e5e-cd67-4266-8a21-9a6ef36a1bd0', passwordVariable: 'pass', usernameVariable: 'user')]) {
                        sh "git push https://$user:$pass@github.com/DeanKelly751/final-pipeline.git main"
                    }
                }
            }
        }
    }
}