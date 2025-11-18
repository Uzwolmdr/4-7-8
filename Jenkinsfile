pipeline {
    agent any

    environment {
        NEWMAN_CMD = "${env.APPDATA}\\npm\\newman.cmd"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out Git repository...'
                checkout scm
            }
        }

        stage('Install Newman') {
            steps {
                echo 'Installing Newman if not installed...'
                bat 'npm install -g newman newman-reporter-htmlextra || echo Newman already installed'
            }
        }

        stage('Run Postman Collection') {
            steps {
                echo 'Executing Postman Collection...'
                bat """
                    if not exist reports mkdir reports
                    ${env.NEWMAN_CMD} run postman\\SampleCollection.json ^
                        -e postman\\SampleEnvironment.json ^
                        --reporters cli,junit,htmlextra ^
                        --reporter-junit-export reports\\test-results.xml ^
                        --reporter-htmlextra-export reports\\report.html
                """
            }
        }
    }

    post {
        always {
            echo 'Archiving reports...'

            // Archive artifacts
            archiveArtifacts artifacts: 'reports/*', allowEmptyArchive: true

            // Publish JUnit test results
            junit 'reports/test-results.xml'

            // Publish HTML report
            publishHTML([
                reportDir: 'reports',
                reportFiles: 'report.html',
                reportName: 'Postman Test Report',
                allowMissing: true,
                keepAll: true
            ])
        }
    }
}
