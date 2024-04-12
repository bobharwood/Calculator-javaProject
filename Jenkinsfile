pipeline {
    agent any
    
    // Define Archivista URL
    def archivistaUrl = 'http://localhost:9000'
    
    // Define file names for build and test stage attestations
    def buildAttestationFile = 'build_attestation.json'
    def testAttestationFile = 'test_attestation.json'
    
    stages {
        stage('Build') {
            steps {
                // Compile the project
                script {
                    def buildSuccess = sh script: 'mvn clean package', returnStatus: true
                    if (buildSuccess == 0) {
                        // Generate attestation for the build step
                        def result = sh(script: 'witness run --step build -o ' + buildAttestationFile + ' -- mvn clean package', returnStdout: true)
                        echo "Generated attestation file: ${buildAttestationFile}"
                        
                        // Store the attestation in Archivista
                        def result = sh(script: 'curl -X POST -F "attestation=@' + buildAttestationFile + '" ' + archivistaUrl, returnStdout: true)
                        echo "Stored attestation for the build stage in Archivista"
                    } else {
                        echo "Build stage failed, skipping attestation generation"
                    }
                }
            }
        }
        stage('Test') {
            steps {
                // Run tests
                script {
                    def testSuccess = sh script: 'mvn test', returnStatus: true
                    if (testSuccess == 0) {
                        // Generate attestation for the test stage
                        def result = sh(script: 'witness run --step test -o ' + testAttestationFile + ' -- mvn test', returnStdout: true)
                        echo "Generated attestation file: ${testAttestationFile}"
                        
                        // Store the attestation in Archivista
                        def result = sh(script: 'curl -X POST -F "attestation=@' + testAttestationFile + '" ' + archivistaUrl, returnStdout: true)
                        echo "Stored attestation for the test stage in Archivista"
                    } else {
                        echo "Test stage failed, skipping attestation generation"
                    }
                }
            }
        }
    }
}
