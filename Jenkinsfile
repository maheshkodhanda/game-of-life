pipeline
	{
		agent {label 'slave2'}
	environment
		{
		sonar_url='http://172.31.12.119:9000'
		sonar_username='admin'
		sonar_password='admin'
		nexus_url='http://172.31.12.119:8081'
		artifact_version='0.0.1'
		}
	tools
		{
		jdk 'java8'
		maven 'maven-3.3.9'
		}
	parameters
	    {
	    string(defaultValue:'master',description:'select branch to build',name:'Branches')
	    
	    }
	stages
		{
		stage('gitclone')
			{
			steps
				{
				git branch:'$Branches',url:'https://github.com/maheshkodhanda/game-of-life.git'
				}
			}
		stage('compile and build')
			{
		    steps
			    {
			        sh '''
		        mvn clean install -U -Dmaven.test.skip=true
		        '''
				}
			}
		stage('Sonarqube Analysis')
			{
			steps
				{
				withSonarQubeEnv('sonarqube')
				{
				sh '''
				mvn clean package org.jacoco:jacoco-maven-plugin:prepare-agent install -Dmaven.test.failure.ignore=false
				mvn -e -B sonar:sonar -Dsonar.java.source=1.8 -Dsonar.host.url="${sonar_url}" -Dsonar.login="${sonar_username}" -Dsonar.password="${sonar_password}" -Dsonar.sourceEncoding=UTF-8
				'''
				}
				}
			}
		stage('Publish Artifact')
			{
			steps
				{
				nexusArtifactUploader artifacts: [[artifactId: 'gameoflife', classifier: '', file: '/var/lib/jenkins/workspace/pipeline-job/gameoflife-build/target/gameoflife-build-1.0-SNAPSHOT.jar', type: 'jar']], credentialsId: '46cdf4cf-2240-470c-ac59-d5740fedad2b', groupId: 'com.wakaleo.gameoflife', nexusUrl: '172.31.12.119:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'game-of-life_versions', version: '4.0.0'	
				}
			}
		
		}
	}
