pipeline{
    agent any
    {
        stage('check out')
        {
            checkout scm
        }
    }
    stages{
        stage('Build')
        {
            echo 'Building'
        }
        stage('Test')
        {
            echo 'Testing'
        }
        stage('deploy')
        {
            echo 'deployed'
        }
    }
}