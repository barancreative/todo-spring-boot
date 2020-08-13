pipeline {
    agent any

    triggers {
        pollSCM('*/5 * * * *')
    }

    stages {
        stage('Compile') {
            steps {
                gradlew('clean', 'classes')
            }
        }
        stage('Unit Tests') {
            steps {
                gradlew('test')
            }
            post {
                always {
                    junit '**/build/test-results/test/TEST-*.xml'
                }
            }
        }
        stage('Long-running Verification') {
            environment {
                SONAR_LOGIN = credentials('SONAR_CUBE_KEY')
            }
            parallel {
                stage('Integration Tests') {
                    steps {
                        gradlew('integrationTest')
                    }
                    post {
                        always {
                            junit '**/build/test-results/integrationTest/TEST-*.xml'
                        }
                    }
                }
                stage('Code Analysis') {
                    steps {
                        //echo "Running place holder for sonarcube"
                        gradlew('sonarqube')
                    }
                }
            }
        }
        stage('Assemble') {
            steps {
                gradlew('assemble')
                stash includes: '**/build/libs/*.war', name: 'app'
            }
        }
        stage('Promotion') {
            steps {
                timeout(time: 1, unit:'DAYS') {
                    input 'Deploy to Production?'
                }
            }
        }
        stage('Deploy to Production') {
            //environment {
                //HEROKU_API_KEY = credentials('HEROKU_API_KEY')
           // }
            steps {
                unstash 'app'
                //gradlew('deployHeroku')
                echo "place holder for deployement done in blue ocean"
            }
        }
    }
    post {
        failure {
            mail to: 'benjamin.muschko@gmail.com', subject: 'Build failed', body: 'Please fix!'
        }
    }
}

def gradlew(String... args) {
    bat label: "function", script: "./gradlew ${args.join(' ')}"
}
