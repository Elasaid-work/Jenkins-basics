pipeline {
    agent any

    tools {
        nodejs 'node-24'
    }

    environment {
        VENV_DIR = "${WORKSPACE}/backend/venv"
    }

    stages {

        

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


        stage('Run Sonarqube') {
            environment {
                scannerHome = tool 'MySonarQube';
            }
            steps {
              withSonarQubeEnv('MySonarQube') {
                sh "${scannerHome}/bin/sonar-scanner"
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
