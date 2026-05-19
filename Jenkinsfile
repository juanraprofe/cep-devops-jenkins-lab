pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timestamps()
        timeout(time: 5, unit: 'MINUTES')
    }

    environment {
        FORCE_COLOR = '0'
        NO_COLOR = 'true'
    }

    stages {
        stage('Audit tools') {
            steps {
                sh 'node --version'
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Generate Prisma client') {
            steps {
                sh 'npm run prisma:generate'
            }
        }

        stage('Format check') {
            steps {
                sh 'npm run format:check'
            }
        }

        stage('Code quality') {
            steps {
                sh 'npm run lint'
            }
        }

        stage('Type check') {
            steps {
                sh 'npm run type-check'
            }
        }

        stage('Tests') {
            steps {
                sh 'npm run test'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
                archiveArtifacts artifacts: 'dist/**', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Review logs.'
        }

        always {
            cleanWs()
        }
    }
}