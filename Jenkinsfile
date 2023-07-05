pipeline{

    agent any

    stages{

        stage("Sonar Code Quality Check"){

            agent{

                docker {
                    image 'maven'
                }
            }
            
            tools {
                maven '3.9.3'
                }
                
            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonarqube') {

                    def mavenHome = tool name: "Maven-3.8.6", type: "maven"
                    def mavenCMD = "${mavenHome}/bin/mvn"
                    sh "${mavenCMD} clean package sonar:sonar"
                    }
                }
            }

        }
    }
}
