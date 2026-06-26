pipeline {
    agent any

    stages {
        /* stage('Build') {
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
        */

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
    }
    post{
        always {
            junit 'jest-results/junit.xml'
        }
    }
}