pipeline {
    agent any

    tools {
        dotnetsdk 'dotnet-9'
    }

    stages{
        stage('Restore'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Restoring dependencies...'
                    sh 'dotnet restore'
                }
            }
        }
        stage('Test'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Running tests...'
                    sh 'dotnet test  --no-build --verbosity normal'
                }
            }
        }
        stage('Build'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Building the project...'
                    sh 'dotnet build --configuration Release --no-restore'
                }
            }
        }
        stage('Publish'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Publishing the project...'
                    sh 'dotnet publish --configuration Release --no-build -o ./publish'
                }
            }
        }
    }
    
    post {
        always {
            echo 'This will always run after the stages.'
        }
    }
}
