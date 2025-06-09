pipeline {
    agent {
        docker {
            image 'gradle:8.4.0-jdk17'
        }
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token-2')
        SONAR_URL = 'http://localhost:9000'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/KritTods/springboot-ci-cd-demo.git'
            }
        }

        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }

        stage('Test & Coverage') {
            steps {
                sh './gradlew test jacocoTestReport'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "./gradlew sonarqube -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }

        stage('Docker Build') {
            agent any
            environment {
                IMAGE_NAME = 'krittod/springboot-ci-cd-demo:1.0.0'
            }
            steps {
                withDockerRegistry(credentialsId: 'docker-hub-creds') {
                    sh '''
                        docker build -t $IMAGE_NAME .
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            agent any
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
}
