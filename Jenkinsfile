pipeline {
    agent any

    stages {
        stage('Build project') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh 'npm install'
            }
        }
    }
}