pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID='6d82f876-e105-4cc5-a241-96a4d4d1bd4f'
        NETLIFY_AUTH_TOKEN=credentials('TOKEN')
        
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
                echo 'small change'
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
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
                    npm test -- --watch=false
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm ci
                    npx serve -s build &
                    sleep 10
                    npx playwright test
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
echo "Deploying to production site id"
node_modules/.bin/netlify status
node_modules/.bin/netlify deploy --dir=build --prod --no-build

                '''
            }
        }
    }
    stage('Prod E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
             environment{
        
        CI_ENVIRONMENT_URL='https://calm-sunburst-dbd2f5.netlify.app'
    }
            steps {
                sh '''
                    
                    npx playwright test
                '''
            }
        }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
