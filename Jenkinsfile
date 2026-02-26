node {
 def mavenHome = tool name: "maven392"

  try
    {
 stage('GIT'){
 echo "from git"
 git branch: 'development', url: 'https://github.com/ravikodavatiganti/maven-webapplication-project-kkfunda.git'
}

stage('Maven Build'){
 echo "from Maven"
 sh "${mavenHome}/bin/mvn clean compile"
}

stage('SonarQube'){
  sh "${mavenHome}/bin/mvn sonar:sonar"
}
stage('Nexus'){
 echo "from Nexus"
 sh "${mavenHome}/bin/mvn clean deploy"
}
stage('Tomcat'){
 echo "from Tomcat"
 sh """
curl -u ravi:ravi \
--upload-file /var/lib/jenkins/workspace/jio-scripted/target/maven-web-application.war \
"http://54.81.131.160:8080/manager/text/deploy?path=/maven-web-application&update=true&war="
"""
  }
 }  //try ending
  catch (e) {
       currentBuild.result = "FAILED"
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
} // node ending

def notifyBuild(String buildStatus = 'STARTED') {
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
  slackSend (color: colorCode, message: summary, channel: '#jio-devteam')
  slackSend (color: colorCode, message: summary, channel: '#jio-devops')
}
