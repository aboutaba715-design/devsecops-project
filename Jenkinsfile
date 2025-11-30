
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

        # Décompressser
        unzip -q dependency-check.zip -d dependency-check

        # Vérifier ce qu'il y a réellement dans le zip
        echo "=== CONTENTS ==="
        ls -R dependency-check

  chmod +x dependency-check/dependency-check/bin/dependency-check.sh

# Lancer le scan
dependency-check/dependency-check/bin/dependency-check.sh \
    --scan . --format HTML --out reports || true
        '''
    }
}

        stage('DAST - OWASP ZAP') {
            steps {
                sh '''
                # Si l'image publique n'est pas accessible, utiliser la version weekly
                docker pull owasp/zap2docker-weekly
                docker run -t owasp/zap2docker-weekly zap-baseline.py -t http://localhost:8089 -r zap_report.html || true
                '''
            }
        }

    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.html,zap_report.html', allowEmptyArchive: true
            cleanWs()
        }
    }
}
