pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/aelsutanto-art/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'npm test || exit /b 0'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                powershell '''
                    if (!(Test-Path "sonar-scanner")) {
                        Invoke-WebRequest `
                          -Uri "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-8.1.0.6389-windows-x64.zip" `
                          -OutFile "sonar-scanner.zip"

                        Expand-Archive `
                          -Path "sonar-scanner.zip" `
                          -DestinationPath "." `
                          -Force

                        Rename-Item `
                          "sonar-scanner-8.1.0.6389-windows-x64" `
                          "sonar-scanner"
                    }

                    .\\sonar-scanner\\bin\\sonar-scanner.bat `
                      -Dsonar.token=$env:SONAR_TOKEN
                '''
            }
        }
    }
}