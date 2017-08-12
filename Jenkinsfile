pipeline {
    agent any

    stages {
        stage('Compile') {
            steps {
                sh './gradlew clean classes'
            }
        }
        stage('Unit Tests') {
            steps {
                sh './gradlew test'
            }
            post {
                always {
                    junit '**/build/test-results/test/TEST-*.xml'
                }
            }
        }
        stage('Integration Tests') {
            steps {
                sh './gradlew integrationTest'
            }
            post {
                always {
                    junit '**/build/test-results/integrationTest/TEST-*.xml'
                }
            }
        }
        stage('Assemble') {
            steps {
                sh './gradlew assemble'
            }
        }
    }
}