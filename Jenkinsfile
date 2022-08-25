pipeline {

    agent {
        docker {
            image 'node:14'
        }
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                docker.image('node:14').withRun('--cgroupns host')
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'npm test'
            }
        }
    }
}
