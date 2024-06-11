pipeline{
    agent any
    stages{
        stage('Sonar Quality Check'){
//            agent{
//                docker {
//                    image 'openjdk:11'
//                }
//            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'soner-token') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
                    }

                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waithForQualityGate()
                        if (qg.status != 'OK') {
                            error "pipeline aborted due to quality gate failure: $(qg.status)"
                        }

                    }
                }
            }
        }
    }
}