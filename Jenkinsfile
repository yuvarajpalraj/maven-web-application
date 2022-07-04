node{
def mavenHome = tool name  : "maven-3.8.5"
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '4')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

echo "job name is: ${env.JOB_NAME}"
echo "Node name is: ${env.NODE_NAME}"
echo "Build number is: ${env.BUILD_NUMBER}"

try{
 sendSlackNotifications('STARTED')
stage('CheckOutCode')
{
git branch: 'development', credentialsId: '76f32731-3d50-42de-89ef-0f7177b66de0', url: 'https://github.com/yuvarajpalraj/maven-web-application.git'
}
stage('Build')
{
sh "${mavenHome}/bin/mvn clean package" 
}

stage ('ExecuteSonarcubeReport')
{
sh "${mavenHome}/bin/mvn sonar:sonar"
}
stage('UploadArtifctintoNexsusServerf')
{
sh "${mavenHome}/bin/mvn deploy"
}
stage('DeployToTomCatServer')
{
sshagent(['3f9fedbc-6f3e-4b02-94b6-9ddfa871a2aa']) 
{
 sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.37.6:/opt/apache-tomcat-9.0.64/webapps"
}
}

}catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    sendSlackNotifications(currentBuild.result)
  }
}


/* 
 def sendSlackNotifications(String buildStatus = 'STARTED') {
  
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
  slackSend (color: colorCode, message: summary, channel: 'devopsstudent')
}
*/
