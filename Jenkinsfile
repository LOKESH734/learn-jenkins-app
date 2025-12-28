pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '6d82f876-e105-4cc5-a241-96a4d4d1bd4f'
        NETLIFY_AUTH_TOKEN = credentials('TOKEN')
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
                    echo "Installing dependencies"
                    npm ci

                    echo "Building React app"
                    npm run build

                    echo "Verifying build output"
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
                    echo "Running unit tests"
                    npm test -- --watch=false || true
                '''
            }
        }

        stage('Deploy to Netlify (TEST)') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Installing Netlify CLI"
                    npm install netlify-cli

                    echo "Deploying build folder to Netlify (TEST / Preview)"
                    node_modules/.bin/netlify deploy \
                      --dir=build \
                      --site=$NETLIFY_SITE_ID \
                      --auth=$NETLIFY_AUTH_TOKEN
                '''
            }
        }
    }

    post {
        always {
            node {
                junit allowEmptyResults: true, testResults: 'jest-results/junit.xml'
            }
        }
    }
}
