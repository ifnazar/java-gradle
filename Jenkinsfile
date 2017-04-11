#!groovy

import groovy.json.JsonSlurper

properties([disableConcurrentBuilds()])

@NonCPS
def gradlew(args)
{
	if (isUnix())
		sh "./gradlew " + args
	else
		bat "gradlew " + args
}

node {
    
    try {
	stage('Checkout') {
		checkout scm
	}
	stage('Setup') {
		    configFileProvider([configFile(fileId: '5c4c550f-ebaf-45fb-8456-3a60cb6bf296', variable: 'custom_gradle_file')]) {
		        sh "echo '\n' >> build.gradle"
		        sh "cat ${env.custom_gradle_file} >> build.gradle"
		        sh "cat build.gradle"
		    }   
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
