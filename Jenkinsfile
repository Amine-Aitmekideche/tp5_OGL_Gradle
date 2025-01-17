pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                script {
                    sh './gradlew test --tests "ExampleTest"'
                    sh './gradlew test'
                }
            }
            post {
                always {
                    junit 'build/test-results/test/*.xml'
                    cucumber 'reports/example-report.json'
                }
            }
        }


        stage('Code Analysis') {
                    steps {
                        withSonarQubeEnv('sonar') {
                            sh './gradlew sonar'
                        }
                    }
        }




        stage('Code Quality') {
                    steps {
                        script {
                            def qualityGate = waitForQualityGate()
                            if (qualityGate.status != 'OK') {
                                error "Pipeline failed a cause de code Quality: ${qualityGate.status}"
                            }
                        }
                    }
        }

        stage('Build') {
            steps {
                script {
                    sh './gradlew jar'
                    sh './gradlew javadoc'
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                    archiveArtifacts artifacts: 'build/docs/javadoc/**/*', fingerprint: true
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh './gradlew publish'
                }
            }
        }
        stage('Email Notification') {
            steps {
                script {
                    currentBuild.result = currentBuild.result ?: 'SUCCESS'
                    if (currentBuild.result == 'SUCCESS') {
                        echo 'Build Success! Sending success notifications...'
                        mail to: 'lm_aitmekideche@esi.dz',
                             subject: "Build Success: ",
                             body: "The build and deployment form jenkiess  was successful."
                    } else {
                        echo 'Sending failure notifications...'
                        mail to: 'lm_aitmekideche@esi.dz',
                             subject: "Build Failed: ",
                             body: "The build for  failed."
                    }
                }
            }
        }


//         slack
        stage('Slack Notification') {
            steps {
                slackSend channel: '#tp5',
                          message: ":rocket: *Deployment completed successfully with jenkess!* :tada:"
            }
        }


    }

}