pipeline {
    agent any
    tools {
        maven 'M2_HOME' // M2_HOME doit être le nom de l'installation Maven configurée dans Jenkins
    }
    stages {
        stage('MAVEN') {
            steps {
                sh "mvn -version" // Affiche la version de Maven
            }
        }
    }
}