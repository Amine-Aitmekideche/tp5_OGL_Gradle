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


        stage('sonar') { // Correction de la majuscule pour correspondre à la norme des sections
                    steps {
                        withSonarQubeEnv('sonar') {
                            sh './gradlew sonar'
                        }
                    }
        }




//         stage('Code Quality') {
//                     steps {
//                         script {
//                             def qualityGate = waitForQualityGate()
//                             if (qualityGate.status != 'OK') {
//                                 error "Pipeline failed due to Quality Gate failure: ${qualityGate.status}"
//                             }
//                         }
//                     }
//         }

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

    }

}