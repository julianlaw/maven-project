pipeline {
	agent {
		node {
			label 'master'
		}
	}	
	parameters([string(defaultValue: '6', description: '', name: 'MAX_ERRORS', trim: false)])
	stages{
		stage('Compilar') {
			steps{
				node(label:'master') {
					checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-julianlaw', url: 'https://github.com/julianlaw/maven-project.git']]])
					sh returnStdout: true, script: 'mvn clean package'
					archiveArtifacts artifacts: '**/*.war', onlyIfSuccessful: true
				}
			}
		}
		stage('checkStyle') {
			steps{
				node(label:'master') {
					sh returnStdout: true, script: 'mvn checkstyle:checkstyle'
					checkstyle canComputeNew: false, canRunOnFailed: true, defaultEncoding: '', failedTotalAll: params.MAX_ERRORS, healthy: '', pattern: '', unHealthy: ''
				}
			}
		}
		stage('Desplegar') {
			agent{
				node{
					label 'Windows'
				}
			}
			steps{
				node(label:'Windows') {
					copyArtifacts filter: '**/*.war', fingerprintArtifacts: true, flatten: true, projectName: 'Pipelineascode', selector: specific('$BUILD_NUMBER'), target: env.TOMCAT_HOME
				}
			}
		}
	}
}
