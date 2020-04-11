
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "artifactory"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    def buildTestInfo
    
    
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
	      } 
    } catch (e) {
    currentBuild.result = "FAILED"
    notifyFailed()
    throw e
    }


    stage('Deploy QA'){
	deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: 'http://18.217.106.165:8080/')], contextPath: 'QAWebapp', war: '**/*.war'
     }
	
	stage('functional-test') {
	    //buildTestInfo = rtMaven.run pom: 'functionaltest/pom.xml', goals: 'test'
	    sh 	'''
		  cd functionaltest
		  mvn test
    		'''
	    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
	}
   stage ('BlazeMeter test'){
    blazeMeterTest(credentialsId: 'Blazemeter', testId: '7867856.taurus', workspaceId: '468392')
  }	
    stage('Publish build info') {
        server.publishBuildInfo buildInfo
	//server.publishBuildInfo buildTestInfo
    } 
}
  def notifySuccessful() {
  slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
  }
  def notifyFailed() {
  slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
   }
	 
