pipeline {
    agent any
    
    environment {
        // Define variables at the top level of the pipeline
        BUILD_ATTESTATION_FILE = 'build-attestation.json'
        TEST_ATTESTATION_FILE = 'test-attestation.json'
        ARCHIVISTA_URL = 'http://localhost:9000/upload'
    }

    stages {
        stage('Preparation') {
            steps {
                script {
                    // Check if witness is installed and accessible
                    if (sh(script: 'which witness', returnStatus: true) != 0) {
                        error "Witness is not installed or not in PATH"
                    }
                }
            }
        }
        stage('Build') {
            steps {
                // Perform build
                sh 'mvn clean package'
                
                // Generate attestation for build
                sh "witness run --step build -o ${env.BUILD_ATTESTATION_FILE} -- mvn clean package"
                
                // Store attestation in Archivista
                script {
                    def result = sh(script: "curl -X POST -F 'attestation=@${env.BUILD_ATTESTATION_FILE}' '${env.ARCHIVISTA_URL}'", returnStdout: true)
                    echo "Uploaded build attestation: ${result}"
                }
            }
        }
        stage('Test') {
            steps {
                // Run tests
                sh 'mvn test'
                
                // Generate attestation for tests
                sh "witness run --step test -o ${env.TEST_ATTESTATION_FILE} -- mvn test"
                
                // Store attestation in Archivista
                script {
                    def result = sh(script: "curl -X POST -F 'attestation=@${env.TEST_ATTESTATION_FILE}' '${env.ARCHIVISTA_URL}'", returnStdout: true)
                    echo "Uploaded test attestation: ${result}"
                }
            }
        }
    }
}
