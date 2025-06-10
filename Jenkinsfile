pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token-id-2')
//         SONAR_URL = 'http://localhost:9000'
        IMAGE_NAME = 'todsaponc/springboot-ci-cd-demo:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KritTods/springboot-ci-cd-demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x gradlew'
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
                withSonarQubeEnv('sonar-jenkin') {
                    sh "./gradlew sonar -Dsonar.token=${SONAR_TOKEN}"
                }
            }
        }

        stage('Docker Build') {
            agent {
                    label 'docker-enabled-agent' // ใช้ node ที่มี Docker
                }
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Docker Push') {
            steps {
                withDockerRegistry([credentialsId: 'sonar-token-id-2', url: 'https://index.docker.io/v1/']) {
                    sh "docker push $IMAGE_NAME"
                    sh 'docker push todsaponc/springboot-ci-cd-demo:latest'
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
