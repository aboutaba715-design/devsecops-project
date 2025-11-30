
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:aboutaba715-design/devsecops-project.git'
            }
        }

        stage('SAST - Bandit') {
            steps {
                sh 'bandit -r . || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devsecops-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 8089:80 devsecops-app'
            }
        }

        stage('Tests') {
            steps {
                sh 'pytest'
            }
        }

       stage('Dependency Scan - OWASP') {
    steps {
        sh '''
        # Télécharger Dependency-Check
        wget -q https://github.com/jeremylong/DependencyCheck/releases/download/v12.1.0/dependency-check-12.1.0-release.zip -O dependency-check.zip

        # Décompresser
        unzip -q dependency-check.zip -d dependency-check

        # Vérifier le contenu
        echo "=== CONTENTS ==="
        ls -R dependency-check

        # Donner la permission d'exécution au script correct
        chmod +x dependency-check/dependency-check/bin/dependency-check.sh

        # Lancer le scan
        dependency-check/dependency-check/bin/dependency-check.sh --scan . --format HTML --out reports || true
        '''
    }
}
// <-- fermeture du bloc stages

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.html,zap_report.html', allowEmptyArchive: true
            cleanWs()
        }
    }
} // <-- fermeture du pipeline

