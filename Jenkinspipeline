node{
    
def mavenHome = tool name: "maven3.8.6"

echo "Jenkins url is: ${env.JENKINS_URL}"
echo "Node Name is: ${env.NODE_NAME}"
echo "Job name is: ${env.JOB_NAME}"


properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

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
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@52.90.134.170:/opt/apache-tomcat-9.0.65/webapps/"    
}

}

}
