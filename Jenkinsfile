def jobnameparts = JOB_NAME.tokenize('/') as String[]
def repositoryname = jobnameparts[1]

pipeline {	
    agent {
        label 'master'
    }
	options {        
		skipDefaultCheckout() // Don't checkout automatically
	}

	stages {

		stage('Checkout') {
					
			steps {
    			script {
    				def scmVars = checkout scm
    				git "$scmVars.GIT_URL"
    				}    			
    		}
    	}
    
    	stage('Build') {				
			// build steps here
			environment {
  				mvnHome = "C:\\Users\\dotun\\Documents\\apache-maven-3.5.4\\bin"
			}
			steps {
				
				script {
				
				  if (env.BRANCH_NAME == 'develop' || env.BRANCH_NAME.startsWith('feature')) {
					echo "Running a snapshot build from branch ${env.BRANCH_NAME}"
					// def mvnHome = tool name: 'M3', type: 'maven'
					// def mvnCMD = "${mvnHome}/bin/mvn"
					// sh "${mvnCMD} package -e"
					bat "%mvnHome%\\mvn package -e"
				  }	
				  else if (env.BRANCH_NAME.startsWith('release') || env.BRANCH_NAME == 'master' ) {
				  	echo "Running a release build from branch ${env.BRANCH_NAME}"
					// sh 'mvn install'
					bat "%mvnHome%\\mvn install -e"
				  }
				  
				  else {				  
				  	try {
            				bat 'exit 1'
        			}
        			catch (Exception e) {
            			echo 'The branch name does not meet Fifth Third branch naming standard'
            			throw e;
        			}
				  }

				  
				}	  
			}
		}
		
		stage('Publish Artifact') {
			steps {
				// input id: 'TargetEnv', message: 'publish artifact?', ok: 'yes', parameters: [choice(choices: ['snapshot', 'release'], description: 'choose binary type', name: 'binary')], submitter: 'admin'
				script {				
					def userInput = input(
						// id: 'TargetEnv', message: 'publish artifact?', ok: 'yes', parameters: [choice(choices: ['snapshot', 'release'], description: 'choose binary type', name: 'binary')], submitter: 'admin'
						// id: 'TargetEnv', message: 'publish artifact?', ok: 'yes', parameters: [choice(choices: ['snapshot', 'release'], description: 'choose binary type', name: 'binary'), choice(choices: ['eastern', 'pacific'], description: 'choose timezone', name: 'timezone')], submitter: 'admin'
					id: 'TargetEnv', message: 'publish artifact?', ok: 'yes', parameters: [choice(choices: ['snapshot', 'release'], description: 'choose binary type', name: 'binary'), choice(choices: ['eastern', 'pacific'], description: 'choose timezone', name: 'timezone'), string(defaultValue: 'Dev', description: 'enter property set', name: 'propset', trim: true)], submitter: 'admin'
					)
					// echo ("Env: "+userInput['binary'])
					// echo ("Env: "+userInput)
					echo "artifact type is ${userInput['binary']}"
					echo "deployment timezone is ${userInput['timezone']}"
					echo "property set for binary is ${userInput['propset']}"
				}				
			}
		}
		
		stage ('Deploy') {
			steps {
				script {
					echo "deploying ${repositoryname} to tomcat server in the cloud"
					sshPublisher(publishers: [sshPublisherDesc(configName: 'cloud-server1', transfers: [sshTransfer(excludes: '', execCommand: 'mv /home/user/opt/tomcat/apache-tomcat-8.5.33/webapps/CounterWebApp.war /opt/tomcat/apache-tomcat-8.5.33/webapps/', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/opt/tomcat/apache-tomcat-8.5.33/webapps', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
			    }
			}
		}
	}
}
