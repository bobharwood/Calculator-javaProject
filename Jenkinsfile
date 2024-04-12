pipeline {
    agent any
    
    // Define variables at the top level of the pipeline
    environment {
        BUILD_ATTESTATION_FILE = 'build-attestation.json'
        TEST_ATTESTATION_FILE = 'test-attestation.json'
        ARCHIVISTA_URL = 'http://localhost:9000/upload'
    }
    
    stages {
        stage('Build') {
            steps {
                // Perform build
                sh 'mvn clean package'
                
                // Generate attestation for build
                sh "witness run --step build -o ${BUILD_ATTESTATION_FILE} -- mvn clean package"
                
                // Store attestation in Archivista
                script {
                    def result = sh(script: "curl -X POST -F 'attestation=@${BUILD_ATTESTATION_FILE}' '${ARCHIVISTA_URL}'", returnStdout: true)
                    echo "Uploaded build attestation: ${result}"
                }
            }
        }
        stage('Test') {
            steps {
                // Run tests
                sh 'mvn test'
                
                // Generate attestation for tests
                sh "witness run --step test -o ${TEST_ATTESTATION_FILE} -- mvn test"
                
                // Store attestation in Archivista
                script {
                    def result = sh(script: "curl -X POST -F 'attestation=@${TEST_ATTESTATION_FILE}' '${ARCHIVISTA_URL}'", returnStdout: true)
                    echo "Uploaded test attestation: ${result}"
                }
            }
        }
    }
}
