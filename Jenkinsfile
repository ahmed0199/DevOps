pipeline {
    agent any // Exécute le pipeline sur n'importe quel agent disponible

    tools {
        // CORRECTION DE SYNTAXE : Assurez-vous d'utiliser la guillemet simple standard (')
        jdk 'JAVA_HOME' 
        maven 'M2_HOME'
    }

    stages {
        stage('Checkout & Clone') {
            steps {
                // Cette étape clone le code. 
                // Jenkins le fait souvent automatiquement, mais la spécifier est plus clair.
                git branch: 'main',
                    url: 'https://github.com/ahmed0199/DevOps.git'
            }
        }
        
        stage('Compile') {
            steps {
                echo 'Démarrage de la compilation du projet Maven...'
                // La commande Maven pour nettoyer et compiler
                sh 'mvn clean compile' 
            }
        }
        
        stage('Unit Tests') {
            steps {
                echo 'Exécution des tests unitaires...'
                // La commande Maven pour exécuter les tests. 
                // Le build échouera ici si les tests ne passent pas.
                sh 'mvn test' 
            }
        }
    }
    
    post {
        // La section 'post' s'exécute après que le job a tenté de s'exécuter
        always {
            echo 'Archivage des artefacts et des rapports de tests...'
            // Archive tous les fichiers de résultats de tests JUnit
            junit '**/target/surefire-reports/TEST-*.xml' 
            
            // Archive le fichier .jar ou .war généré si la compilation a réussi
            // archiveArtifacts artifacts: 'target/*.jar, target/*.war', onlyIfSuccessful: true 
        }
        failure {
            // Envoyez une notification en cas d'échec
            echo 'Le pipeline a échoué. Veuillez vérifier les logs.'
            // mail to: 'votre_email@example.com', subject: 'ECHEC du Pipeline CI'
        }
    }
}