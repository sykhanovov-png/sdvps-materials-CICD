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
                echo '=== Docker version ==='
                sh 'docker --version'
            }
        }

        stage('Test') {
            steps {
                echo '=== Running go test ==='
                sh 'go test .'
            }
        }

        stage('Build') {
            steps {
                echo '=== Building Docker image ==='
                sh "docker build -t sdvps-go-app:v${BUILD_NUMBER} ."
                sh "docker tag sdvps-go-app:v${BUILD_NUMBER} sdvps-go-app:latest"
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
