pipeline {
    agent { label 'macHost' }

    environment {
        BUILD_ATTESTATION_FILE = 'build-attestation.json'
        TEST_ATTESTATION_FILE = 'test-attestation.json'
        ARCHIVISTA_URL = 'http://localhost:8082/v1/upload' // Adjusted to the correct API endpoint
    }

    stages {
        stage('Preparation') {
            steps {
                script {
                    if (sh(script: 'which witness && which curl', returnStatus: true) != 0) {
                        error "Required tools are not installed or not in PATH"
                    }
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
                sh "witness run --step build -o ${env.BUILD_ATTESTATION_FILE} -- mvn clean package"

                script {
                    // Using 'curl' to POST the attestation JSON file to Archivista
                    def result = sh(script: "curl -X POST -H 'Content-Type: application/json' --data-binary @${env.BUILD_ATTESTATION_FILE} ${env.ARCHIVISTA_URL}", returnStdout: true)
                    echo "Upload response: ${result}"
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
                sh "witness run --step test -o ${env.TEST_ATTESTATION_FILE} -- mvn test"

                script {
                    def result = sh(script: "curl -X POST -H 'Content-Type: application/json' --data-binary @${env.TEST_ATTESTATION_FILE} ${env.ARCHIVISTA_URL}", returnStdout: true)
                    echo "Upload response: ${result}"
                }
            }
        }
    }
}
