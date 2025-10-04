pipeline {
    agent any
    tools {
        nodejs 'nodejs-2260'
    }

    environment {
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh '''
                    npm install --no-audit
                '''
            }
        }
        stage('Dependency scanning') {
            parallel {
                stage('NPM Dependencies Audit') {
                    steps {
                        sh '''
                            npm audit --audit-level=critical
                            echo $?
                        '''  
                    }
                }
                stage('OWASP Dependency Check') {
                    steps {
                        dependencyCheck additionalArguments: '''
                        --scan \'./\'
                        --format \'ALL\'
                        -- out \'./\'
                        --prettyPrint''', odcInstallation: 'OWASP-Dep1213'

                        dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report.xml', stopBuild: true

                        junit allowEmptyResults: true, testResults: 'dependency-check-junit.xml'

                        publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: './', reportFiles: 'dependency-check-jenkins.html', reportName: 'Dependency HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
            }
        }
        stage('Unit Testing') {
            steps {
                    withCredentials([usernamePassword(credentialsId: 'mongo-db-creds', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                        sh 'npm test'
                    }    
                    
                    junit allowEmptyResults: true, testResults: 'test-results.xml'
                }
            }
        stage('Code Coverage') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo-db-creds', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'OOPPPSSS !! IS THIS FAILED !! SORRY !!! WILL BE FIXING IT SOOOONNNN !!!!', stageResult: 'UNSTABLE') {
                        sh 'npm run coverage'
                    }
                }
                publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Code Coverage HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
        }
    }
}
