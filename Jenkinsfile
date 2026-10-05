pipeline {
    agent any

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {
        stage('Checkout') {
            steps {
                echo '=== Checking out repository ==='
                checkout scm
            }
        }

        stage('Verify environment') {
            steps {
                echo '=== Go version ==='
                sh 'go version'
            }
        }

        stage('Test') {
            steps {
                echo '=== Running go test ==='
                sh 'go test .'
            }
        }

        stage('Build binary') {
            steps {
                echo '=== Building Go binary ==='
                sh '''
                    set -e
                    CGO_ENABLED=0 GOOS=linux go build -a -installsuffix nocgo -o sdvps-app .
                    ls -lh sdvps-app
                '''
            }
        }

        stage('Upload to Nexus') {
            steps {
                echo '=== Uploading binary to Nexus ==='
                sh '''
                    set -e
                    set +x
                    curl -v -f -u "${NEXUS_USER}:${NEXUS_PASS}" \
                         --upload-file sdvps-app \
                         "${NEXUS_URL}/repository/sdvps-raw/sdvps-app-v${BUILD_NUMBER}"
                '''
            }
        }
    }

    post {
        success {
            echo '=== BUILD SUCCESS ==='
        }
        failure {
            echo '=== BUILD FAILED ==='
        }
        always {
            echo "=== Build #${BUILD_NUMBER} finished with status: ${currentBuild.currentResult} ==="
        }
    }
}
