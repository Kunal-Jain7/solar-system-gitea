pipeline {
    agent any
    tools {
        nodejs 'nodejs2260'
    }
    stages {
        stage('Node JS version') {
            steps {
                sh 'node -v'
                sh 'npm -v'
            }
        }
        stage('Install NodeJs Dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }
        stage('Running Dependencies Scan') {
            parallel {
                stage('NPM Dependencies Audit check') {
                    steps {
                        sh '''
                            npm audit --audit-level=critical
                            echo $?
                        '''
                    }
                }
                stage('OWASP Dependencies Check') {
                    steps {
                        dependencyCheck additionalArguments: '''
                            --scan ./
                            --format ALL
                            --out ./
                            --prettyPrint''', odcInstallation: 'owasp1003'
                        dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report.html', stopBuild: true
                    }
                }
            }
        }    
    }
}
