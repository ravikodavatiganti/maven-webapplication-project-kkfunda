node {

    def mavenHome = tool name: "maven392"
    echo "git branch name: ${env.BRANCH_NAME}"
    echo "build number is: ${env.BUILD_NUMBER}"
    echo "node name is: ${env.NODE_NAME}"

    try {

        notifyBuild("STARTED")

        stage('GIT') {
            echo "from git"
            git branch: 'development',
                url: 'https://github.com/ravikodavatiganti/maven-webapplication-project-kkfunda.git'
        }

        stage('Maven Build') {
            echo "from Maven"
            sh "${mavenHome}/bin/mvn clean compile"
        }

        stage('SonarQube') {
            sh "${mavenHome}/bin/mvn sonar:sonar"
        }

        stage('Nexus') {
            echo "from Nexus"
            sh "${mavenHome}/bin/mvn clean deploy"
        }

        stage('Tomcat') {
            echo "from Tomcat"
            sh """
            curl -u ravi:ravi \
            --upload-file target/maven-web-application.war \
            "http://54.81.131.160:8080/manager/text/deploy?path=/maven-web-application&update=true&war="
            """
        }

        currentBuild.result = "SUCCESS"

    } catch (err) {

        if (err instanceof org.jenkinsci.plugins.workflow.steps.FlowInterruptedException) {
            currentBuild.result = "ABORTED"
        } else {
            currentBuild.result = "FAILED"
        }

        throw err

    } finally {
        notifyBuild(currentBuild.result)
    }
}


def notifyBuild(String buildStatus = 'STARTED') {

    buildStatus = buildStatus ?: 'SUCCESS'

    def colorCode = '#FF0000'
    def summary = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"

    if (buildStatus == 'STARTED') {
        colorCode = '#FFFF00'
    }
    else if (buildStatus == 'SUCCESS') {
        colorCode = '#00FF00'
    }
    else if (buildStatus == 'ABORTED') {
        colorCode = '#FFA500'
    }
    else {
        colorCode = '#FF0000'
    }

    slackSend(
        color: colorCode,
        message: summary,
        channel: '#jio-dev'
    )
}
