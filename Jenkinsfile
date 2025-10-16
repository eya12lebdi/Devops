pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo '🔍 Clonage du dépôt...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🔧 Construction du projet...'
                sh 'echo "Build réussi !"'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Exécution des tests...'
                sh 'echo "Tests réussis !"'
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Déploiement...'
                sh 'echo "Déploiement terminé !"'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline exécutée avec succès !'
        }
        failure {
            echo '❌ Échec de la pipeline.'
        }
    }
}
