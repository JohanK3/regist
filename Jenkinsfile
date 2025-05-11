pipeline {
    agent any

    tools {
        maven 'Maven3'      // Doit être configuré dans Jenkins
        jdk 'Java17'        // Doit être configuré dans Jenkins
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
                // Ajoute credentialsId: 'github' si repo privé
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
    }
}
