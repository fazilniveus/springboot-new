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
        stage("Quality gate") {
            steps {
                def qualitygate = waitForQualityGate()
                if (qualitygate.status != "OK") {
                    error "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}"
                 }
                    
            }
        

        }
    }
}
