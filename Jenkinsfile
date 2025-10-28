pipeline {
    agent any
    
    tools {
        nodejs 'nodejs25'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'local-dev', url: 'https://github.com/harsh2478/3-Tier-DevSecOps-Mega-Project.git'
            }
        }
        stage('Frontend Compilation') {
            steps {
                dir('client'){
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
        stage('Backend Compilation') {
            steps {
                dir('api'){
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
        stage('Gitleaks Scan'){
            steps{
                sh 'gitleaks detect --source ./client --exit-code 1'
                sh 'gitleaks detect --source ./api --exit-code 1'
                sh 'echo $?'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' ${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=3Tier-DevSecOps \
                    -Dsonar.projectName=3Tier-DevSecOps \
                    -Dsonar.branch.name=local-dev '''
                }
            }
        }
        stage('Quality Gate Check') {
            steps {
                timeout(time: 1, unit: 'HOURS'){
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Trivy File System Check') {
            steps {
                sh 'trivy fs --format table -o fs-report.html .'
            }
        }
    }
}
