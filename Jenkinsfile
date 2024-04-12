pipeline {
    agent any
    
    // Define file names
    def buildAttestationFile = 'build-attestation.json'
    def testAttestationFile = 'test-attestation.json'
    
    // Define Archivista URL
    def archivistaUrl = 'http://localhost:9000/upload'
    
    stages {
        stage('Build') {
            steps {
                // Perform build
                sh 'mvn clean package'
                
                // Generate attestation for build
                sh "witness run --step build -o ${buildAttestationFile} -- mvn clean package"
                
                // Store attestation in Archivista
                def result = sh(script: 'curl -X POST -F "attestation=@' + buildAttestationFile + '" ' + archivistaUrl, returnStdout: true)
                echo "Uploaded build attestation: ${result}"
            }
        }
        stage('Test') {
            steps {
                // Run tests
                sh 'mvn test'
                
                // Generate attestation for tests
                sh "witness run --step test -o ${testAttestationFile} -- mvn test"
                
                // Store attestation in Archivista
                result = sh(script: 'curl -X POST -F "attestation=@' + testAttestationFile + '" ' + archivistaUrl, returnStdout: true)
                echo "Uploaded test attestation: ${result}"
            }
        }
    }
}
