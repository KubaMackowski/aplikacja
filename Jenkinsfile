pipeline {
    agent any

    environment {
        scannerHome = tool 'SonarQube'
        DOCKER_HUB_PASSWORD = credentials('DOCKER_HUB_PASSWORD')
        SONARQUBE_TOKEN = credentials('SONARQUBE_TOKEN')
    }

    stages {
        stage('Clear running apps') {
            steps {
                sh 'docker rm -f devops_flask_app || true'
            }
        }
        stage('Sonarqube analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.login=${SONARQUBE_TOKEN}"
                }
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
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
                sh "docker login -u jmackowski -p ${DOCKER_HUB_PASSWORD}"
                sh "docker tag devops_flask_app:${BUILD_NUMBER} jmackowski/devops_flask_app:${BUILD_NUMBER}"
                sh 'docker tag devops_flask_app:latest jmackowski/devops_flask_app:latest'
                sh "docker push jmackowski/devops_flask_app:${BUILD_NUMBER}"
                sh 'docker push jmackowski/devops_flask_app:latest'
            }
        }
    }
}
