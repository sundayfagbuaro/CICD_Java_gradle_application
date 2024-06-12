pipeline{
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
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
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "pipeline aborted due to quality gate failure: ${qg.status}"
                        }

                    }
                }
            }
        }
        stage('Docker Build && Docker Push'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker-nexus-cred', variable: 'docker-nexus-password')]) {
                         sh '''                      
                            docker build -t 192.168.1.167:8083/spingapp:${VERSION} .
                            docker login -u admin -p $docker-nexus-password 192.168.1.167:8083 
                            docker push 192.168.1.167:8083/spingapp:${VERSION}
                            docker rmi 192.168.1.167:8083/spingapp:${VERSION}    
                         '''
                    }                       
                }                      
            }
        }
    }
}
