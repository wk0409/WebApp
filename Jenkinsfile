
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "artifactorywk"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    def functest
    
 rtMaven.tool = "maven"

 // tools {
   //       maven 'maven'
     //   }
	
   stage('Clone sources') {
      git url: 'https://github.com/wk0409/webapp.git'
    }

    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }

	 stage('Maven Build') {
		buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
			 // post {
       //always {
	       echo 'echo jiraSendBuildInfo site: balajisubramanian.atlassian.net'
           jiraSendBuildInfo branch: 'master', site: 'balajisubramanian.atlassian.net'
	 }
  
	
  

	//stage('Code Analysis'){
		//withMaven(maven: 'maven') {
			//withSonarQubeEnv(credentialsId: 'sonarvidhusecretkey') {
			//sh 'mvn -B -DskipTests package sonar:sonar -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
		//}
			//}
		//}
		

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
	
	
	stage ('delpoy to QA'){
	deploy adapters: [tomcat7(credentialsId: 'tomcatusername', path: '', url: 'http://18.191.105.236:8080')], contextPath: '/QAwebapp', war: '**/*.war'
    
		echo 'echo jiraSendDeploymentInfo site: balajisubramanian.atlassian.net'
           //jiraSendDeploymentInfo site: 'balajisubramanian.atlassian.net', environmentId: 'us-qa-1', environmentName: 'us-qa-1', environmentType: 'Quality'
	jiraSendDeploymentInfo environmentId: 'QA', environmentName: 'Quality', environmentType: 'staging', serviceIds: [''], site: 'balajisubramanian.atlassian.net', state: 'successful'
	}
	
	
	//stage('Functional Test'){
		//functest = rtMaven.run pom: 'functionaltest/pom.xml', goals: 'test'
		//publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
	//}

//stage ('performance testing'){
	//blazeMeterTest credentialsId: 'wk-blaze-key', testId: '7854009.taurus', workspaceId: '464414'
	//}
	
	//stage ('deploy to PROD'){
		//deploy adapters: [tomcat7(credentialsId: 'tomcatusername', path: '', url: 'http://18.189.21.155:8080')], 
			//contextPath: '/ProdwebApp', war: '**/*.war'
	 
	      // echo 'echo jiraSendDeploymentInfo site: balajisubramanian.atlassian.net'
           //jiraSendDeploymentInfo site: 'balajisubramanian.atlassian.net', environmentId: 'us-prod-1', environmentName: 'us-prod-1', environmentType: 'production'
   //}	
	
	//stage('Acceptance Test'){
		//functest = rtMaven.run pom: 'Acceptancetest/pom.xml', goals: 'test'
		//publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\Acceptancetest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
	//slackSend channel: '#devops-bcamp', 
		//color: 'good', 
		//message: 'Pipeline Job Executed Successfully', 
		//tokenCredentialId: 'wk-slack'
	//}

}
