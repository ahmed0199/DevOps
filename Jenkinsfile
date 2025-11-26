pipeline {
    agent any // Exécute le pipeline sur n'importe quel agent disponible

    environment {
        // Variables nécessaires pour la construction et le push Docker
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_USERNAME = 'ahmedallaya'
        IMAGE_NAME = "alpine"
        // Le tag sera la date et l'heure de la construction
        IMAGE_TAG = sh(returnStdout: true, script: "date +%Y%m%d%H%M%S").trim()
        // ID de l'identifiant Docker Hub stocké dans Jenkins
        DOCKER_CREDENTIAL_ID = 'docker-hub-credentials' 
    }

    stages {
        // Étape 1 : Récupération du Code (Checkout)
        stage('Récupération du Code') {
            steps {
                // Récupère les dernières mises à jour du dépôt Git
                git branch: 'main', url: '' 
                echo "Code récupéré."
            }
        }

        // Étape 2 : Nettoyage et Reconstruction du Projet
        stage('Nettoyage & Build Projet') {
            steps {
                // Exemple pour un projet Maven/Java.
                // Adaptez ces commandes à votre technologie (npm install, etc.)
                sh 'mvn clean install'
                echo "Projet reconstruit et nettoyé."
            }
        }

        // Étape 3 : Construction de l'Image Docker
        stage('Construction Docker') {
            steps {
                script {
                    // Construction de l'image locale avec le tag complet
                    def fullImageName = "${DOCKER_REGISTRY}/${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                    
                    // Commande équivalente à docker build -t <tag_complet> .
                    sh "docker build -t ${fullImageName} ." 
                    echo "Image Docker construite: ${fullImageName}"
                }
            }
        }

        // Étape 4 : Publication (Push) vers Docker Hub
        stage('Publication Registre') {
            steps {
                // Utilise les identifiants stockés dans Jenkins pour l'authentification
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIAL_ID, 
                                                passwordVariable: 'DOCKER_PASSWORD', 
                                                usernameVariable: 'DOCKER_USER')]) {
                    
                    def fullImageName = "${DOCKER_REGISTRY}/${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                    
                    // Commande équivalente à docker login + docker push
                    sh "docker push ${fullImageName}"
                    echo "Image publiée sur Docker Hub."
                }
            }
        }
    }
}