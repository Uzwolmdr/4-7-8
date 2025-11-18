pipeline {
    agent any

    stages {
        stage('Install Newman') {
            steps {
                echo "Installing Newman if not installed"
                bat '''
                npm install -g newman newman-reporter-htmlextra || echo Newman already installed
                '''
            }
        }

        stage('Run Postman Collection') {
            steps {
                echo "Executing Postman Collection"
                bat '''
                REM Create reports folder
                if not exist reports mkdir reports

                REM Run Postman collection with environment
                newman run postman\\SampleCollection.json ^
                    -e postman\\SampleEnvironment.json ^
                    --reporters cli,junit,htmlextra ^
                    --reporter-junit-export reports\\test-results.xml ^
                    --reporter-htmlextra-export reports\\report.html
                '''
            }
        }
    }

    post {
        always {
            echo "Archiving reports..."
            archiveArtifacts artifacts: 'reports/*', allowEmptyArchive: true
            junit 'reports/test-results.xml'
            publishHTML([
                reportDir: 'reports',
                reportFiles: 'report.html',
                reportName: 'Postman Test Report',
                alwaysLinkToLastBuild: true
            ])
        }
    }
}