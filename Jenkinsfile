pipeline{

    agent any

    stages{

        stage("Sonar Code Quality Check"){

            agent{

                docker {
                    image 'maven:3.9.0-eclipse-temurin-11' 
                    args '-v /root/.m2:/root/.m2' 
                }
            }
            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonarqube') {

                            sh 'mvn clean package sonar:sonar'
                        }
                }
            }
            stage("Quality Gate Status"){

                steps{

                    script{
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube'
                    }
                }
            }

        }
    }
}