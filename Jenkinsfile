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

          echo "Starting app"
          npx serve -s build &
          sleep 10

          echo "Running Playwright E2E tests"
          npx playwright test --reporter=junit
        '''
    }
}
