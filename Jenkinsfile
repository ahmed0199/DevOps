pipeline {
    agent any

    environment {
        REGISTRY = "docker.io"
        IMAGE_NAME = "ahmedallaya/devops"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS = 'docker-hub-credentials'
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Récupération du dépôt Git..."
                checkout scm
            }
        }

        stage('Clean Workspace') {
            steps {
                echo "Nettoyage du workspace..."
                sh 'git clean -fdx'
            }
        }

        stage('Build Project') {
            steps {
                echo "Build Maven..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Build image Docker..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Push Docker Hub..."
                withCredentials([
                    usernamePassword(
                        credentialsId: DOCKER_CREDENTIALS,
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout
                    """
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonarTOK', variable: 'SONAR_TOKEN')]) {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=mon-projet \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=$SONAR_TOKEN
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl apply -f mysql-deployment.yaml
                    kubectl apply -f spring-deployment.yaml
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline terminé avec succès 🎉"
        }
        failure {
            echo "Le pipeline a échoué ❌"
        }
    }
}
