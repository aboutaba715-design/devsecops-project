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
}

    }
}
