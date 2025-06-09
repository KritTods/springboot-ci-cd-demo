pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token-2')
        SONAR_URL = 'http://localhost:9000'
        IMAGE_NAME = 'krittod/springboot-ci-cd-demo:1.0.0'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KritTods/springboot-ci-cd-demo.git'
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
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Docker Push') {
            steps {
                withDockerRegistry([credentialsId: 'sonar-token-2', url: 'https://index.docker.io/v1/']) {
                    sh "docker push $IMAGE_NAME"
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
