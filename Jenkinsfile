pipeline {
    agent any

    environment {
        DOCKER_HUB_PASSWORD = credentials('DOCKER_HUB_PASSWORD')
    }

    stages {
        stage('Clear running apps') {
            steps {
                sh 'docker rm -f devops_flask_app || true'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t devops_flask_app:${BUILD_NUMBER} -t devops_flask_app:latest ."
            }
        }
        stage('Run app') {
            steps {
                sh "docker run -d -p 0.0.0.0:5555:5555 --name devops_flask_app -t devops_flask_app:${BUILD_NUMBER}"
            }
        }
        stage('Upload backend Docker Image to Docker Hub') {
            steps {
                sh "docker login -u devopstkhtechnology -p ${DOCKER_HUB_PASSWORD}"
                sh "docker tag devops_flask_app:${BUILD_NUMBER} devopstkhtechnology/devops_flask_app:${BUILD_NUMBER}"
                sh 'docker tag devops_flask_app:latest devopstkhtechnology/devops_flask_app:latest'
                sh "docker push devopstkhtechnology/devops_flask_app:${BUILD_NUMBER}"
                sh 'docker push devopstkhtechnology/devops_flask_app:latest'
            }
        }
    }
}
