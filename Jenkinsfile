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
                SONARQUBE_SCANNER = tool 'MySonarQube'
                SONAR_LOGIN = credentials('sonar-token-id')
                GIT_USERNAME = credentials('git-username-id')
                GIT_PASSWORD = credentials('git-password-id')
            }
            steps {
                sh '''
                sed -i "s/__SONAR_LOGIN__/$SONAR_LOGIN/" sonar-project.properties
                sed -i "s/__GIT_USERNAME__/$GIT_USERNAME/" sonar-project.properties
                sed -i "s/__GIT_PASSWORD__/$GIT_PASSWORD/" sonar-project.properties
                '''
                withSonarQubeEnv('MySonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
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
