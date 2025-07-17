pipeline {
    agent any

    tools {
        nodejs 'node-24'
    }

    environment {
        VENV_DIR = "${WORKSPACE}/backend/venv"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/Elasaid-work/Jenkins-basics.git'
            }
        }

        stage('Install Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm run build'
                }
            }
        }

        stage('Setup Python Backend') {
            steps {
                dir('backend') {
                    sh 'python3 -m venv venv'
                    sh '. venv/bin/activate && pip install -r requirements.txt'
                }
            }
        }

        stage('Run Backend Tests') {
            steps {
                dir('backend') {
                    sh '. venv/bin/activate && pytest'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQube') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build & Code Analysis Successful'
        }
        failure {
            echo '❌ Build failed or Quality Gate not passed'
        }
    }
}
