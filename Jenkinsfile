pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    environment {
        APP_NAME = "registre-pipeline-ci"
        RELEASE = "1.0.0"
        DOCKER_USER = "johankarl"
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/JohanK3/regist.git'
                // Ajoute credentialsId si dépôt privé
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test Application') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-token-sonar') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker_image = docker.build("${IMAGE_NAME}")

                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push("latest")
                    }
                }
            }
        }
    }
}
