pipeline {
    agent any
    tools{
        dotnet 'dotnet-9'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'dotnet --version'
                // Add your build steps here
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                // Add your test steps here
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // Add your deployment steps here
            }
        }
    }

    post {
        always {
            echo 'This will always run after the stages.'
        }
    }
}
