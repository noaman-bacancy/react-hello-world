
pipeline {
    agent { docker { image 'node:14' } }
    stages {
        stage('build') {
            steps {
                sh 'node --version'
                sh 'npm install'
            }
        }
    }
}

