pipeline {
    agent any 

    environment {
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_USERNAME = 'ahmedallaya'
        IMAGE_NAME = "alpine"
        IMAGE_TAG = sh(returnStdout: true, script: "date +%Y%m%d%H%M%S").trim()
        DOCKER_CREDENTIAL_ID = 'docker-hub-credentials' // ID créé dans Gérer les Identifiants
    }

    stages {
        stage('Récupération du Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ahmed0199/DevOps.git' 
                echo "Code récupéré."
            }
        }

        stage('Nettoyage & Build Projet') {
            steps {
                sh 'mvn clean install' // Exemple pour Maven/Java
                echo "Projet reconstruit et nettoyé."
            }
        }

        stage('Construction Docker') {
            steps {
                script {
                    def fullImageName = "${DOCKER_REGISTRY}/${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                    
                    sh "docker build -t ${fullImageName} ." 
                    echo "Image Docker construite: ${fullImageName}"
                }
            }
        }

        stage('Publication Registre') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIAL_ID, 
                                                passwordVariable: 'DOCKER_PASSWORD', 
                                                usernameVariable: 'DOCKER_USER')]) {
                    
                    script { 
                        def fullImageName = "${DOCKER_REGISTRY}/${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
                        
                        
                        sh "docker push ${fullImageName}"
                        echo "Image publiée sur Docker Hub."
                    } 
                    // ---------------------------------------------------------------
                }
            }
        }
    }
}