pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { git 'https://github.com/<your-repo>' }
        }

        stage('Install & Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t sample-app:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 3000:3000 --name sample-app sample-app:latest'
            }
        }
    }

    post {
        always {
            emailext to: 'admin@example.com',
            subject: "Build ${currentBuild.currentResult}",
            body: "Build completed with status: ${currentBuild.currentResult}"
        }
    }
}
