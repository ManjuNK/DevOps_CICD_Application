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
                        withEnv(['DATREE_TOKEN=421f97fc-7dae-49c7-b278-6a75804336c0']) {

                        sh 'helm datree test .'
                        }
                    }
                }
            }
       }
       stage("Pushing the Helm chart to Nexus Repo"){

        steps{
            script{
                withCredentials([string(credentialsId: 'nexus-password', variable: 'nexus-creds')]) {
                    dir('kubernetes/'){
                        
                        sh '''
                        helmversion=$(helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                        tar -czvf myapp-${helmversion}.tgz /myapp
                        curl -u admin:$nexus-creds http://3.64.237.2:8081/repository/Helm-Hosted-Repo/ --upload-file myapp-${helmversion}.tgz -v'
                        '''
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