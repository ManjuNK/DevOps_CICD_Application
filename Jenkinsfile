pipeline{

    agent any

    stages{

        stage("Sonar Code Quality Check"){

            agent{

                docker {
                    image 'maven'
                }
            }
            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonarqube') {

                            def mavenHome = tool name: "Maven-3.9.3", type: "maven"
                            def mavenCMD = "${mavenHome}/bin/mvn"
                            sh "${mavenCMD} clean package sonar:sonar"
                        }
                }
            }

        }
    }
}
