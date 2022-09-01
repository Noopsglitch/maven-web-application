node{
    
def mavenHome = tool name: "maven3.8.6"

echo "Jenkins url is: ${env.JENKINS_URL}"
echo "Node Name is: ${env.NODE_NAME}"
echo "Job name is: ${env.JOB_NAME}"


properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

try{
    
slackNotifications('STARTED')
    
stage('CheckOutCode'){
git branch: 'development', credentialsId: 'a4266f3f-9e7e-41c0-87ce-e681403882a3', url: 'https://github.com/Noopsglitch/maven-web-application.git'
}

stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage('ExcecuteSonarQubeReport'){
sh "${mavenHome}/bin/mvn clean sonar:sonar"
}

stage('UploadArtifactsIntoArtifactoryRepo'){
sh "${mavenHome}/bin/mvn clean deploy"
}

stage('DeployIntoTomcatServer'){
sshagent(['312212c1-da22-4b79-bc8b-89c6ec8780e7']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.83.217:/opt/apache-tomcat-9.0.65/webapps/"    
}

}

}//try block closing

catch (e) {
slackNotifications(currentBuild.result)
throw e
}
finally{
slackNotifications(currentBuild.result)
}

}//node closing


//Code snippet for sending slack notifications


def slackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
