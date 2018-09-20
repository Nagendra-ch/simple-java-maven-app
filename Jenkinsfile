pipeline {
	agent {
		docker {
			image 'maven:3-alpine'
			args '-v /root/.m2:/root/.m2'
		}
	}
	stages {
		stage('Build') {
			steps {
				sh 'mvn -B -DskipTests clean package'
			}
		}
		stage('Test') {
			steps {
				sh 'mvn test'
			}
			post {
				always {
					junit 'target/surefire-reports/*.xml'
				}
			}
		}
		stage('Dependency Check') {
			steps {
				dependencyCheckAnalyzer datadir: 'dependency-check-data', includeVulnReports: true, hintsFile: '', includeCsvReports: false, includeHtmlReports: true, includeJsonReports: false, isAutoupdateDisabled: false, outdir: '', scanpath: '', skipOnScmChange: false, skipOnUpstreamChange: false, suppressionFile: '', zipExtensions: ''
                
    		        	dependencyCheckPublisher canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''

            			archiveArtifacts allowEmptyArchive: true, artifacts: '**/dependency-check-report.html'
                
                    		archiveArtifacts allowEmptyArchive: true, artifacts: '**/dependency-check-report.xml'
			}
		}
		stage('Deliver') {
			steps {
				archiveArtifacts allowEmptyArchive: true, artifacts: '**/*.jar'
				sh './jenkins/scripts/deliver.sh'
			}
		}
		stage('Push To Nexus') {
			steps {
				sh 'mvn deploy:deploy-file -DgroupId=com.devsecops -DartifactId=simple-java-maven-app -Dversion=1.0.0 -DgeneratePom=false -Dpackaging=jar -DrepositoryId=nexus -Durl=http://johndavidmarx3.mylabserver.com:8081/repository/maven-releases -Dfile=target/my-app-1.0-SNAPSHOT.jar'
			}
		}
	}
}
