pipeline {
    agent any

    tools {
        maven 'Maven3'       // Maven configuré dans Jenkins
        jdk 'Java17'         // JDK configuré dans Jenkins
    }

    environment {
        APP_NAME = "registre-ci"
        RELEASE = "1.0.0"
        DOCKER_USER = "johankarl"
        DOCKER_CREDENTIALS_ID = "dockerhub"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {

        stage('Nettoyage de l\'espace de travail') {
            steps {
                cleanWs()
            }
        }

        stage('Récupération du code source') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/JohanK3/regist.git'
                // Ajouter credentialsId: 'github-token' si le dépôt est privé
            }
        }

        stage('Compilation Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Exécution des tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Analyse SonarQube') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-token-sonar') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage('Construction et Push de l\'image Docker') {
            steps {
                script {
                    def dockerImage = docker.build("${IMAGE_NAME}")

                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        dockerImage.push("${IMAGE_TAG}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Analyse de sécurité avec Trivy') {
            steps {
                script {
                    sh """
                        docker run --rm \
                        -e TRIVY_TIMEOUT=10m \
                        -v /var/run/docker.sock:/var/run/docker.sock \
                        aquasec/trivy image ${IMAGE_NAME}:latest \
                        --no-progress \
                        --scanners vuln \
                        --exit-code 0 \
                        --severity HIGH,CRITICAL \
                        --format table
                    """
                }
            }
        }

        stage('Nettoyage des artefacts Docker') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
                    sh "docker rmi ${IMAGE_NAME}:latest || true"
                }
            }
        }
    }
}
