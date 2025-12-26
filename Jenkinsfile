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

                  echo "Checking build output"
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
                  echo "Running Jest unit tests"
                  npm test -- --watch=false
                '''
            }
        }

        stage('E2E Tests') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  echo "Installing dependencies"
                  npm ci

                  echo "Installing Playwright browsers"
                  npx playwright install --with-deps

                  echo "Starting React app"
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
            echo "Publishing test reports"
            junit 'test-results/junit.xml'
        }
    }
}
