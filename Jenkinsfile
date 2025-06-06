pipeline {
    agent {
      docker {
        image 'gradle:8.4.0-jdk17' // หรือเวอร์ชันที่ต้องการ
      }
    }

    tools {
        gradle 'gradle-8'
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token')
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
            steps {
                sh 'docker build -t springboot-ci-cd-demo:latest .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
}