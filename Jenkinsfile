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
        DOCKER_CREDENTIAL_ID = 'docker-hub-credentials' // L'ID créé
    }

    stages {
        // Étape 1 : Récupération du Code (Checkout)
        stage('Récupération du Code') {
            steps {
                // ASSUREZ-VOUS QUE L'URL EST CORRECTE
                git branch: 'main', url: 'https://github.com/ahmed0199/DevOps.git' 
                echo "Code récupéré."
            }
        }

        // Étape 2 : Nettoyage et Reconstruction du Projet
        stage('Nettoyage & Build Projet') {
            steps {
                // ADAPTEZ CETTE COMMANDE : si votre projet n'est pas Maven, remplacez par une autre commande ou un simple echo
                sh 'mvn clean install' 
                echo "Projet reconstruit et nettoyé."
            }
        }

        // Étape 3 : Construction de l'Image Docker
        stage('Construction Docker') {
            steps {
                script {
                    // La déclaration 'def' est bien dans un bloc 'script {}'
                    def fullImageName = "${DOCKER_REGISTRY}/${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                    
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
                    
                    // --- CORRECTION CRUCIALE : Le bloc 'script' englobe la déclaration 'def' ---
                    script { 
                        // La déclaration 'def' est maintenant valide
                        def fullImageName = "${DOCKER_REGISTRY}/${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                        
                        // NOTE: Les variables DOCKER_USER et DOCKER_PASSWORD sont injectées dans l'environnement du shell
                        // par le withCredentials, mais docker push réussit généralement sans docker login explicite
                        // si les credentials sont correctement exposés via le plugin Docker Pipeline.
                        sh "docker push ${fullImageName}"
                        echo "Image publiée sur Docker Hub."
                    } 
                    // -------------------------------------------------------------------------
                }
            }
        }
    }
}