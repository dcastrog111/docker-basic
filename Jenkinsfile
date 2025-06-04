pipeline {
    agent any

    tools {
        dotnetsdk 'dotnet-9'
        nodejs 'node-20'
    }

    stages{
        stage('Check versions'){
            steps {
                script {
                    echo 'Node.js version:'
                    sh 'node -v'
                    echo 'NPM version:'
                    sh 'npm -v'
                    echo 'Dotnet version:'
                    sh 'dotnet --version'
                    
                    def dotnetSdkPath = tool 'dotnet-9'
                    echo "Dotnet SDK (tool 'dotnet-9') path provided by Jenkins: ${dotnetSdkPath}"
                    sh 'echo "Initial DOTNET_ROOT before specific stage: $DOTNET_ROOT"'
                    sh 'echo "PATH before specific stage: $PATH"'
                    sh 'dotnet --list-runtimes' // Ver qué runtimes ve el 'dotnet' global
                    
                    sh 'dotnet tool list --global || echo "No dotnet global tools listed or command failed."'
                    sh 'ls -la ${env.HOME}/.dotnet/tools || echo "${env.HOME}/.dotnet/tools not found."'
                }
            }
        }
        stage('Backend - Restore') {
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Restoring dependencies...'
                    sh 'dotnet restore'
                }
            }
        }

        stage('Backend - Static Analysis') {
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Running static analysis...'
                    withSonarQubeEnv('SonarQube') {
                        def dotnetSdkToolPath = tool 'dotnet-9'
                        
                        withEnv([
                            "PATH+DOTNET_TOOLS=${env.HOME}/.dotnet/tools",
                            "DOTNET_ROOT=${dotnetSdkToolPath}"
                        ]) {
                            echo "--- Environment for SonarScanner ---"
                            sh 'echo "DOTNET_ROOT is now: $DOTNET_ROOT"'
                            sh 'echo "PATH is now: $PATH"'
                            sh 'which dotnet'
                            sh 'dotnet --version' // Verificar versión de dotnet en este contexto
                            sh 'dotnet --list-runtimes'
                            echo "--- End Environment for SonarScanner ---"
                            
                            sh 'dotnet sonarscanner begin /k:"Docker-Basic" /d:sonar.host.url="http://192.168.67.129:9000" /d:sonar.login="squ_1af08e6da05e5db1d4682685c630e06dd0f3da64"'
                            sh 'dotnet build'
                            sh 'dotnet sonarscanner end /d:sonar.login="squ_1af08e6da05e5db1d4682685c630e06dd0f3da64"'
                        }
                    }
                }
            }
        }
        stage('Backend - Test'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Running tests...'
                    sh 'dotnet test  --no-build --verbosity normal'
                }
            }
        }
        stage('Backend - Code Coverage'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Running code coverage...'
                    sh 'dotnet test --collect:"XPlat Code Coverage" --no-build --verbosity normal'
                }
            }
        }
        stage('Backend - Build'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Building the project...'
                    sh 'dotnet build --configuration Release --no-restore'
                }
            }
        }
        stage('Backend - Publish'){
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Publishing the project...'
                    sh 'dotnet publish --configuration Release --no-build -o ./publish'
                }
            }
        }
        stage('Frontend - Install'){
            steps {
                dir('10-net9-remix-pg-env/Frontend') {
                    echo 'Installing dependencies...'
                    sh 'npm install'
                }
            }
        }
        stage('Frontend - Test'){
            steps {
                dir('10-net9-remix-pg-env/Frontend') {
                    echo 'Running tests...'
                    sh 'npm test'
                }
            }
        }
        stage('Frontend - Build'){
            steps {
                dir('10-net9-remix-pg-env/Frontend') {
                    echo 'Building the project...'
                    sh 'npm run build'
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
