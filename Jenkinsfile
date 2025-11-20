pipeline {
    agent any 

    
    stages {
        stage('Test Connexion Git') {
            steps {
                echo 'Pipeline demarre et code clone.'
                
                sh 'uname -a' 
            }
        }
        
        stage('Hello World') {
            steps {
                sh 'echo "CI Pipeline OK - Hello World!"' 
            }
        }
    }
}