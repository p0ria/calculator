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
                script {
                    withCredentials([usernamePassword(credentialsId: '3292f693-f1f0-4711-8fb6-937274180bbe', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                        sh "docker login -u ${env.DOCKER_HUB_USERNAME} -p ${env.$DOCKER_HUB_PASSWORD}"
                        sh 'docker push p0ria/calculator'
                    }
                }
            }
        }
    }
}
