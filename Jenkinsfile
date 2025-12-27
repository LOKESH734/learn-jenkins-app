pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '6d82f876-e105-4cc5-a241-96a4d4d1bd4f'
        NETLIFY_AUTH_TOKEN = credentials('Token')
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
                    npm ci
                    npm run build
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
                    npm test -- --watch=false || true
                '''
            }
        }

        stage('Deploy (TEST)') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    npx netlify deploy \
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
