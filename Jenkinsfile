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
        sh 'pytest'  // Exécute pytest pour lancer tes tests
    }
}    stage('Dependency Scan - OWASP') {
            steps {
                sh '''
                # Télécharger et exécuter OWASP Dependency-Check
                wget -q https://github.com/jeremylong/DependencyCheck/releases/download/v8.5.1/dependency-check-8.5.1-release.zip -O dependency-check.zip
                unzip -q dependency-check.zip -d dependency-check
                ./dependency-check/dependency-check.sh --scan . --format HTML --out reports || true
                '''
            }
                  stage('DAST - OWASP ZAP') {
            steps {
                sh '''
                docker pull owasp/zap2docker-stable
                docker run -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:8080 -r zap_report.html || true
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
        

    }
}
