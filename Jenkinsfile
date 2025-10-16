pipeline {
    agent any

   tools {
    maven 'M2_HOME'   // correspond au nom que tu as configuré
    jdk 'JAVA_HOME'   // ou le nom de ton JDK configuré
}


    environment {
        // Variables d'environnement
        APP_NAME = "springboot-app"
    }

    stages {

        stage('Checkout') {
            steps {
                echo '🔍 Clonage du dépôt GitHub...'
                git branch: 'main', url: 'https://github.com/eya12lebdi/Devops.git'
            }
        }

        stage('Build') {
            steps {
                echo '🔧 Construction du projet avec Maven...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Exécution des tests unitaires...'
                sh 'mvn test'
            }
        }

        stage('Archive Artifact') {
            steps {
                echo '📦 Archivage du fichier JAR...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Déploiement de l’application...'
                // Exemple : copier le jar vers un serveur distant
                // sh 'scp target/*.jar user@serveur:/chemin/deploiement'
                echo 'Déploiement simulé ✅'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline exécutée avec succès !'
        }
        failure {
            echo '❌ Erreur dans la pipeline.'
        }
    }
}
