pipeline {
    agent any

    environment {
        REGISTRY = "docker.io"          // Ton registre (ex: Docker Hub)
        IMAGE_NAME = "ahmedallaya/devops" // Nom de ton image
        IMAGE_TAG = "latest"            // Tag de l'image
        DOCKER_CREDENTIALS = 'docker-hub-credentials' // ID des credentials Jenkins
    }

    triggers {
        // Le webhook GitHub déclenche déjà, mais on met un fallback
        pollSCM('* * * * *') // Vérifie chaque minute au cas où
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
                echo "Reconstruction du projet..."
                sh ' mvn clean package -DskipTests'
                    
                       
                    

            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Construction de l’image Docker..."
                sh """
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Connexion & push sur le registre..."
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS,
                                                 usernameVariable: 'USER',
                                                 passwordVariable: 'PASS')]) {
                    sh """
                        echo "$PASS" | docker login -u "$USER" --password-stdin ${REGISTRY}
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout ${REGISTRY}
                    """
                }
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
