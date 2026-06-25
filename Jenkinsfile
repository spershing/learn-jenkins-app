pipeline {
    agent any

    stages {
        stage('Build') {
            /*
            This is a multi line comment which reflects fact
            that the agent section below dictates the configuration
            of the agent, in this case a Docker container running
            NodeJS on an alpine linux
            */
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
                    image 'mcr.microsoft.com/playwright:v1.61.0-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install serve
                node_modules/.bin/serve -s build
                npx playwright test
                '''
            }
        }
    }
    post{
        always {
            junit 'test-results/junit.xml'
        }
    }
}