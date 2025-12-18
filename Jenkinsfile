pipeline {
    agent any

    environment {
        // Docker
        REGISTRY = "docker.io"
        IMAGE_NAME = "ahmedallaya/devops"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS = "docker-hub-credentials"

        // SonarQube
        SONAR_HOST_URL = "http://localhost:9000"
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                echo "📥 Checkout du dépôt Git"
                checkout scm
            }
        }

        stage('Clean Workspace') {
            steps {
                echo "🧹 Nettoyage du workspace"
                sh 'git clean -fdx'
            }
        }

        stage('Build Maven') {
            steps {
                echo "⚙️ Build Maven"
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Build image Docker"
                sh """
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "📤 Push vers Docker Hub"
                withCredentials([
                    usernamePassword(
                        credentialsId: DOCKER_CREDENTIALS,
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin ${REGISTRY}
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout ${REGISTRY}
                    """
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "🔍 Analyse SonarQube"
                withCredentials([
                    string(credentialsId: 'sonarTOK', variable: 'SONAR_TOKEN')
                ]) {
                    sh """
                        mvn sonar:sonar \
                          -Dsonar.projectKey=student-management \
                          -Dsonar.projectName=student-management \
                          -Dsonar.host.url=${SONAR_HOST_URL} \
                          -Dsonar.login=$SONAR_TOKEN
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "☸️ Déploiement Kubernetes"
                sh """
                    kubectl apply -f mysql-deployment.yaml
                    kubectl apply -f spring-deployment.yaml
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline terminé avec succès 🎉"
        }
        failure {
            echo "❌ Pipeline échoué"
        }
    }
}
