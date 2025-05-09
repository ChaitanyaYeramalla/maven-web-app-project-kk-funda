node {

   echo "git branch name: ${env.BRANCH_NAME}"
   echo "build number is: ${env.BUILD_NUMBER}"
   echo "node name is: ${env.NODE_NAME}"
    
    def mavenHome=tool name: "Maven3.9,9"

    try {
    notifyBuild('started')
	stage('git checkout')
	{
       git branch: 'prod',
            credentialsId: '25a1b0e5-e71d-44f0-b966-6ad8e6c41a93',
            url: 'https://github.com/ChaitanyaYeramalla/maven-web-app-project-kk-funda'
	}
	stage('compile')
	{
    sh  "${mavenHome}/bin/mvn compile"
	}
	stage('Build')
	{
    sh  "${mavenHome}/bin/mvn clean package"
	}
   stage('SQ Report')
	{
    sh  "${mavenHome}/bin/mvn sonar:sonar"
	}
	stage('Deploy into Nexus')
	{
    sh  "${mavenHome}/bin/mvn clean deploy"
	}
	stage('Deploy to Tomcat') {
        echo "Deploying WAR file using curl..."

        sh """
            curl -u chaitanya:4267@Chaitu \
            --upload-file /var/lib/jenkins/workspace/Jio-scripted-Pl/target/maven-web-application.war \
            "http://3.82.216.212:8080//manager/text/deploy?path=/maven-web-application&update=true"
        """
    }

  } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

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
  slackSend (color: colorCode, message: summary)
}
