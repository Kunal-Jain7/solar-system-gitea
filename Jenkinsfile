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
    }
}