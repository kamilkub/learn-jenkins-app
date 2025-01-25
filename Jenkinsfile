pipeline {
	agent any

	environment {
		NETLIFY_SITE_ID = '58670b9a-635b-4d2d-9402-0ff4304758a0'
	}


	stages {
		// This is a comment
		stage('Build project') {
			agent {
				docker {
					image 'node:18-alpine'
					reuseNode true
				}
			}
			steps {
				sh '''
                    npm ci
                    npm run build
                '''
			}
		}

		stage('Test') {
			agent {
				docker {
					image 'node:18-alpine'
					reuseNode true
				}
			}

			steps {
				sh '''
                    echo 'Test stage'
                    test -d build
                    npm test
                '''
			}
		}

        stage('Deploy') {
            agent {
				docker {
					image 'node:18-alpine'
					reuseNode true
				}
			}
			steps {
				sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                '''
			}
		}


	}
}