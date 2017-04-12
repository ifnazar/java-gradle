#!groovy

import groovy.json.JsonSlurper

properties([disableConcurrentBuilds()])

def projectName = "xx_projectName"
def projectKey  = "xx_projectKey"

@NonCPS
def gradlew(args)
{
	if (isUnix())
		sh "./gradlew " + args
	else
		bat "gradlew " + args
}

@NonCPS
def appendAdditionalCommand(fileName, additionalCustomCommands) {
	def file = new File(fileName)
	file.append('\n\n')
	file.append(additionalCustomCommands)
}

node {
    
    try {
	stage('Checkout') {
		checkout scm
	}
	stage('Setup') {

		def url = "${env.URL_GRADLE_ADDITIONAL_CUSTOM_COMMANDS}";
		def additionalCustomCommands = new URL(url).getText();
		additionalCustomCommands = additionalCustomCommands.replaceAll( '##sonar.projectName##', "${projectName}" )
		additionalCustomCommands = additionalCustomCommands.replaceAll( '##sonar.projectKey##', "${projectKey}" )

		appendAdditionalCommand("build.gradle", additionalCustomCommands) ;
		gradlew 'clean'
	}
	stage('Build') {
		try {
			gradlew 'build -x test'	
		} catch (exc) {
			onError()
			throw exc
		}
	}
	stage('Test') {
		try {
			gradlew 'test'
		} catch (exc) {
			onError()
			throw exc
		} finally {
			
		}
		stage('Sonar') {
			gradlew "sonarqube -Dsonar.buildbreaker.queryMaxAttempts=90 -Dsonar.buildbreaker.skip=true -Dsonar.host.url=http://10.42.11.93:9000"
		}
	}
    }
    finally {
		stage('Cleanup') {
			dir(workspace) {
				deleteDir();
			}
		}
	}


}
