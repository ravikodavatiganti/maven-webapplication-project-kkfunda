node {
 def mavenHome = tool name: "maven392"
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
}
