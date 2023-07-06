pipeline{

    agent any
    environment {
        VERSION = "${env.BUILD_ID}"
    }

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

        }
        stage("Quality Gate Status"){

            steps{

                script{
                    
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube'
                }
            }
        }
       
        stage("Docker Build and Docker Push to Nexus Repo"){
            
            steps{
                
                script{
                    
                    withCredentials([string(credentialsId: 'nexus-password', variable: 'nexus-creds')]) {
                        sh '''
                        docker build -t 3.64.237.2:8083/springapp:${VERSION} .

                        docker login -u admin -p admin 3.64.237.2:8083

                        docker push 3.64.237.2:8083/springapp:${VERSION}

                        docker rmi 3.64.237.2:8083/springapp:${VERSION}
                        '''
                    }
                }
            }
       }
       stage("identifying misconfigs using datree in helm charts"){

            steps{
                script{
                    dir('kubernetes/myapp/') {
                        withEnv(['DATREE_TOKEN=b993dda8-1f88-4169-a238-db8dca9d1357']) {

                        sh 'helm datree test .'
                        }
                    }
                }
            }
       }
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "nkmanju412@gmail.com";  
		}
	}
}