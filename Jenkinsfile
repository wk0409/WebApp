
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
		 if (buildInfo='SUCCESS'){
			 slackSend channel: '#devops-bcamp', 
				 color: 'good', 
				 message: 'Build Success', 
				 tokenCredentialId: 'wk-slack'
		 }
	}
  
  		
	stage('Code Analysis'){
		withMaven(maven: 'maven') {
			withSonarQubeEnv(credentialsId: 'sonarvidhusecretkey') {
			sh 'mvn -B -DskipTests package sonar:sonar -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
		}
			}
		}
		
		

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
	
	
	stage ('delpoy to QA'){
	deploy adapters: [tomcat7(credentialsId: 'tomcatusername', path: '', url: 'http://18.191.105.236:8080')], contextPath: '/QAwebapp', war: '**/*.war'
    }
	
	stage('Functional Test'){
		functest = rtMaven.run pom: 'functionaltest/pom.xml', goals: 'test'
		publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
	}
	
	//stage ('performance testing')
	///{
	//blazeMeterTest credentialsId: 'wk-blaze-key', testId: '7854009.taurus', workspaceId: '464414'
	//}
}
	 
