pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'ef4c1883-ebfb-4403-ace8-4aa98a6f8fa3'
    }

    stages {
        stage('Build') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                ls -la 
                node --version
                npm --version
                #The following line is required for running NPM in CICD pipeline
                npm ci
                npm run build
                ls -la
                '''
            }
        }
        stage('Run Tests') {
            parallel {
                stage('Test') {
                    agent{
                        docker{
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        echo "Test Stage Beginning"
                        test -f build/index.html
                        npm test
                        '''
                    }
                    post{
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }
                stage('E2E') {
                    agent{
                        docker{
                            image 'mcr.microsoft.com/playwright:v1.39.0-focal'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        npm install serve
                        node_modules/.bin/serve -s build &
                        sleep 15
                        npx playwright test --reporter=html
                        '''
                    }
                }
            }
        }
                stage('Deploy') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install netlify-cli@20.1.1
                npx netlify --version
                echo "Deploying Site ID $NETLIFY_SITE_ID to production."
                '''
            }
        }
    }
}