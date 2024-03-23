pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // sh label: "npm install", script: "npm i"
            }
        }
        parallel {
            stages {
                stage("SonarQube") {
                    environment {
                        scannerHome = tool 'KPMG-Test'
                    }
                    steps {
                        withSonarQubeEnv('KPMG-Test') {
                            sh '''
                            ${scannerHome}/bin/sonar-scanner \
                            -D sonar.projectKey=juice-shop \
                            -D sonar.projectName="Juice Shop" \
                            -D sonar.projectVersion=0.1 \
                            -D sonar.sources=./
                            '''
                        }
                    }
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}