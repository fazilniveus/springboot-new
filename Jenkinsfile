pipeline{
    agent any
    tools {
        maven 'Maven'
    }
    stages{
       stage('GetCode'){
            steps{
                checkout scm
            }
       }        
       stage('Build'){
            steps{
                sh 'mvn clean'
                
            }
       }
       stage('SonarQube analysis') {
        	steps{
        		withSonarQubeEnv('sonarqube-6.5') { 
        			sh "mvn test -Dtest=TestControllerTests  -DfailIfNoTests=false"
        			sh "mvn clean install sonar:sonar -Dsonar.login=admin -Dsonar.password=admin"
    			}
        	}
        }
        stage("Quality Gate"){
            steps{
                script{
                
                    //timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
                        def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    //}
               }
            }

        }
    }
}
