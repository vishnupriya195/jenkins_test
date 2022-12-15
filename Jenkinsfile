properties([pipelineTriggers([githubPush()])])

pipeline {
    agent any
    
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MVN3"
		jdk "jdk8"
    }

    stages {
        stage('Pullscm') {
            steps {
                // Get some code from a GitHub repository
                git credentialsId: 'github', url: 'git@github.com:sathishbob/jenkins_test.git'
			}
		}
		
		stage('print') {
		    agent { label 'linux' }
		    steps {
		        sh "echo testing running a stage on node"
		    }
		}
			
		stage('build') {
			steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true -f api-gateway clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit 'api-gateway/target/surefire-reports/*.xml'
                    archiveArtifacts artifacts: 'api-gateway/target/*.jar', followSymlinks: false
                }
            }
        }
    }
}
