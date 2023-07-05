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

                            sh "mvn clean package sonar:sonar"
                        }
                }
            }

        }
    }
}