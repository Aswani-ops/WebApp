
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "artifactory"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
   
    
 rtMaven.tool = "maven"

    stage('Clone sources') {
        git url: 'https://github.com/Aswani-ops/webapp.git'
    }
    
    //stage('Code Quality') { 
                         //def scannerHome = tool 'SonarQubeScanner';
                          //withSonarQubeEnv("sonarqube") {
                          //sh "${tool("SonarQubeScanner")}/bin/sonar-scanner"
                               //        }
                             //  }
                     

    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }
  try {
    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
   notifySuccessful()
	      } catch (e) {
    currentBuild.result = "FAILED"
    notifyFailed()
    throw e
  }
    }
  def notifySuccessful() {
  slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
  }
  def notifyFailed() {
  slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
   }

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }

    }
	 
