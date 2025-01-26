pipeline {
	agent any

	environment {
		NETLIFY_SITE_ID = '58670b9a-635b-4d2d-9402-0ff4304758a0'
		NETLIFY_AUTH_TOKEN = credentials('netlify-token')

	}


	stages {
		stage('Build') {
			agent {
				docker {
					image 'node:18-alpine'
					reuseNode true
				}
			}
			steps {
				sh '''
                    ls -la
                    echo "Small change"
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
			}
		}

		stage('Test') {
		    parallel {
		        stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            #test -f build/index.html
                            npm test
                        '''
                    }

                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
               }

               stage('E2E tests') {
                   agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                   }

                   steps {
                       sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                       '''
                   }



                   post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
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
                      node_modules/.bin/netlify status
                      node_modules/.bin/netlify deploy --dir=build --prod
                    '''
              }
              }

              stage('E2E Production Tests') {
                  agent {
                       docker {
                           image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                           reuseNode true
                       }
                  }

                  environment {
                    CI_ENVIRONMENT_URL = 'https://singular-syrniki-6399c9.netlify.app'
                  }

                  steps {
                      sh '''
                           npx playwright test --reporter=html
                      '''
                  }

                  post {
                       always {
                           publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Production HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                       }
                  }
              }
		    }
		}

	}
}