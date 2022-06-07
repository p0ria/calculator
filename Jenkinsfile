pipeline {
    agent any
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('Compile') {
            steps {
                sh 'chmod +x gradlew'
                sh './gradlew compileJava'
            }
        }

        stage('Unit test') {
            steps {
                sh './gradlew test'
            }
        }

        stage('Code coverage') {
            steps {
                sh './gradlew jacocoTestReport'
                publishHTML(target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: 'JaCoCo Report'
                ])
                sh './gradlew jacocoTestCoverageVerification'
            }
        }

        stage('Static code analysis') {
            steps {
                sh './gradlew checkstyleMain'
            }
            post {
                always {
                    publishHTML(target: [
                        reportDir: 'build/reports/checkstyle/',
                        reportFiles: 'main.html',
                        reportName: 'Checkstyle Report'
                    ])
                }
            }
        }

        stage('Package') {
            steps {
                sh './gradlew build'
            }
        }

        stage('Docker build') {
            steps {
                sh 'docker build -t p0ria/calculator .'
            }
        }

        stage('Docker push') {
            steps {
                sh 'docker push p0ria/calculator'
            }
        }
    }
}
