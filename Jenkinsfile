pipeline {
    agent any

    environment {
        SYFT_URL = 'https://raw.githubusercontent.com/anchore/syft/main/install.sh'
        GRYPE_URL = 'https://raw.githubusercontent.com/anchore/grype/main/install.sh'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Syft') {
            steps {
                sh 'curl -sSfL $SYFT_URL | sh -s -- -b /usr/local/bin'
            }
        }

        stage('Generate SBOM') {
            steps {
                sh 'syft dir:. -o cyclonedx-json > sbom.json'
            }
        }

        stage('Install Grype') {
            steps {
                sh 'curl -sSfL $GRYPE_URL | sh -s -- -b /usr/local/bin'
            }
        }

        stage('Scan for Vulnerabilities') {
            steps {
                sh 'grype sbom:sbom.json -o table > vulnerabilities.txt || true'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'sbom.json, vulnerabilities.txt', fingerprint: true
            }
        }
    }
    post {
        always {
            echo 'Build completed'
        }
    }
}