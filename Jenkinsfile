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

def appendExtraCommand(fileName, additionalCommandFile) {
    def value = '';
    if (fileExists(fileName)) {
        value = readFile(fileName);
    }

    def additionalCommand = readFile(additionalCommandFile);
    value += '\n\n'+ additionalCommand;

    writeFile file: fileName, text: value
}

node {
    
    try {
	stage('Checkout') {
		checkout scm
	}
	stage('Setup') {
		    def customAdditionalCommands = new URL("https://raw.githubusercontent.com/ifnazar/java-gradle/master/custom.gradle").getText();
		    appendExtraCommand("build.gradle", customAdditionalCommands) ;
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
