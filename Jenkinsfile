node{
def mavenHome = tool name  : "maven-3.8.5"
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '4')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
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
}
