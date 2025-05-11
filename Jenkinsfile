pipeline {
    agent any

    tools {
        maven 'Maven3'       // Maven configuré dans Jenkins (nom global tools)
        jdk 'Java17'         // JDK 17 configuré dans Jenkins (nom global tools)
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
                // Ajoute credentialsId: 'github' si le dépôt est privé
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
    }
}
