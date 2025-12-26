pipeline {
    agent any

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
                  echo "Installing dependencies"
                  npm ci

                  echo "Building React app"
                  npm run build

                  echo "Validating build"
                  test -f build/index.html
                '''
            }
        }

        stage('Unit Tests') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  echo "Running Jest tests"
                  npm test -- --watch=false
                '''
            }
        }

        stage('E2E Tests') {
            agent {
                docker {
                    // This image ALREADY has browsers + deps
                    image 'mcr.microsoft.com/playwright:v1.57.0-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  echo "Installing app dependencies"
                  npm ci

                  echo "Starting React build"
                  npx serve -s build &
                  sleep 10

                  echo "Running Playwright E2E tests"
                  npx playwright test --reporter=junit
                '''
            }
        }
    }

    post {
        always {
            echo "Publishing JUnit report"
            junit 'test-results/junit.xml'
        }
    }
}
