def SendEmailNotification(String result) {
  
    // config 
    def to = emailextrecipients([
           requestor()
    ])
    
    // set variables
    def subject = "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} ${result}"
    def content = '${JELLY_SCRIPT,template="html"}'

    // send email
    if(to != null && !to.isEmpty()) {
        env.ForEmailPlugin = env.WORKSPACE
        emailext mimeType: 'text/html',
        body: '${FILE, path="/home/mohammad_fazil/jacoco.zip"}',
        subject: currentBuild.currentResult + " : " + env.JOB_NAME,
        to: to, attachLog: true
    }
}

def SendEmailNotificationDependency(String result) {
  
    // config 
    def to = emailextrecipients([
           requestor()
    ])
    
    // set variables
    def subject = "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} ${result}"
    def content = '${JELLY_SCRIPT,template="html"}'

    // send email
    if(to != null && !to.isEmpty()) {
        env.ForEmailPlugin = env.WORKSPACE
        emailext mimeType: 'text/html',
        body: '${FILE, path="/home/mohammad_fazil/jacoco.zip"}',
        subject: currentBuild.currentResult + " : " + env.JOB_NAME,
        to: to, attachLog: true
    }
}

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
        			sh """
              mvn clean verify sonar:sonar \
              -Dsonar.projectKey=sonar-jenkins \
              -Dsonar.host.url=http://34.93.141.200:9000 \
              -Dsonar.login=6d6599c9efa9a9e013bd29c1ea98bfaa27b08f5a
              
              """
    			}
        	}
        }
        stage('Quality'){
            steps{
                script{
                    sleep(10)
                    //qualitygate = waitForQualityGate()
                    //if (qualitygate.status != "OK") {
                       // currentBuild.result = "FAILURE"
                       // slackSend (channel: '****', color: '#F01717', message: "*$JOB_NAME*, <$BUILD_URL|Build #$BUILD_NUMBER>: Code coverage threshold was not met! <http://****.com:9000/sonarqube/projects|Review in SonarQube>.")
                    //}
                
                    waitForQualityGate abortPipeline: true                    
                }
            }
       }
      stage('Zip the Report Folder'){
            steps{
              script{
                sh "sudo apt install zip unzip"
                //sh "cd /var/lib/jenkins/workspace/sonar-email/target/site/"
                sh "sudo zip jacoco.zip /var/lib/jenkins/workspace/sonar-email/target/site/jacoco"
              }
            }
       }
       stage('Email'){
            steps{
                SendEmailNotification(currentBuild.result)
                
            }
       }
      stage('Dependency Check') {
        	steps{
        		withSonarQubeEnv('sonarqube-6.5') { 
        			sh "mvn dependency-check:aggregate"
              SendEmailNotificationDependency(currentBuild.result)
    			}
        	}
      }
      
        
    }
}
