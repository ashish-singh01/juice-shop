pipeline {
    agent any

    stages {
        stage('Clean Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }

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

        stage("Owasp ZAP") {
            steps {
                sh label: "Installl ZAP", script: '''
                ls
                wget https://github.com/zaproxy/zaproxy/releases/download/v2.14.0/ZAP_2.14.0_Linux.tar.gz
                tar -xf *.tar.gz
                '''
                // sh label: "Start application", script: "npm install && npm run serve"
                sh label: "Start application", script: '''
                docker build . -t juice-shop -f ./Dockerfile
                docker run -d -p 3000:3000 juice-shop
                '''


                sh label: "Perform ZAP test", script: "./ZAP_2.14.0/zap.sh -quickurl 'http://localhost:3000' -port 8088 -quickprogress -quickout ${WORKSPACE}/report.xml -cmd"
            }

            post {
                success {
                    archiveArtifacts artifacts: 'report.xml', fingerprint: true
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        reportDir: '',
                        keepAll: true,
                        reportFiles: 'report.xml',
                        reportName: "ZAP Report"
                    ])
                }
                always {
                    script {
                        containers = sh(returnStdout: true, script: "docker ps -a -q")
                        sh """
                        docker stop ${containers}
                        docker rm ${containers}
                        """
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