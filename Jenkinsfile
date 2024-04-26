pipeline {
   agent {
	   label 'linux'
   }
	
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MVN3"
    }

    stages {
	    stage ('scm') {
		    steps {
			    // Get some code from a GitHub repository
                git credentialsId: 'github', url: 'git@github.com:sathishbob/jenkins_test.git'
				}
			}
	    stage ('print stage') {
		    steps {
			    sh 'echo "new stage"'
		    }
	    }
        stage('Build') {
            steps {
               // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true -f api-gateway clean package"

                // To run Maven on a Windows agent, use
                //bat "mvn -Dmaven.test.failure.ignore=true -f api-gateway clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit stdioRetention: '', testResults: 'api-gateway/target/surefire-reports/*.xml'
                    archiveArtifacts 'api-gateway/target/*.jar'
                }
            }
        }
	 stage('sonar analysis') {
	       steps {
		       script {
			       scannerHome = tool 'sonar';
			       withSonarQubeEnv('sonar') {
						sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=jenkins_test -Dsonar.projectName=jenkins_test -Dsonar.projectVersion=1.0 -Dsonar.projectBaseDir=$WORKSPACE -Dsonar.sources=$WORKSPACE -Dsonar.java.binaries=$WORKSPACE -Dsonar.exclusions='OWASP-Dependency-Check/**, dastreport/**, vapt/**, report/**'"
						}
				}
			}
	   }
	    stage('sonar quality gate') {
		    steps {
			    script {
				    sleep(40)
				    qg = waitForQualityGate()
				    if (qg.status != 'OK') {
					    error "pipeline aborted due to quality gate failure: ${qg.status}"
				    }
			    }
		    }
	    }
	    stage('dependency check') {
		    steps {
			    script {
				    sh '''
					sudo rm -rf $PWD/report/* || true
     					sudo chmod -R 777 $PWD
     					sudo mkdir -p $PWD/OWASP-Dependency-Check/data
	 				sudo mkdir -p $PWD/report
      					sudo chmod 777 $PWD/OWASP-Dependency-Check/data
	   				sudo chmod 777 $PWD/report
					sudo docker run --rm -v $PWD:/src:z -v $PWD/OWASP-Dependency-Check/data:/usr/share/dependency-check/data:z -v $PWD/report:/report:z owasp/dependency-check --scan /src --format "ALL"  --project "devsecops" --out /report '''
			    }
		    }
	    	post {
		    success {
			    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
		    }
	    	}
	  }
	    stage('sca quality gate') {
		    steps {
			    script {
				    def criticaloutput = sh(script: 'cat report/dependency-check-report.xml | grep -i critical | wc -l', returnStdout: true).trim()
				    def criticalnumber = criticaloutput.toInteger()
				    def criticalthreshold = 15
				    if( criticalnumber > criticalthreshold) {
					    error("SCA failled, so aborting the build")
				    }
			    }
		    }
	    }
	    stage('DAST') {
		    steps {
			    script {
				    sh '''
					sudo rm -rf $PWD/dastreport/* || true
	 				sudo mkdir -p $PWD/dastreport
	   				sudo chmod 777 $PWD/dastreport
					sudo docker run --rm -v $PWD/dastreport:/zap/wrk:rw -t softwaresecurityproject/zap-stable zap-baseline.py -t https://www.labasservice.com -m 1 -d -r dast.html -x dast.xml || true'''
			    }
		    }
	    	post {
		    success {
			    publishHTML target: [
              				allowMissing: false,
              				alwaysLinkToLastBuild: true,
              				keepAll: true,
              				reportDir: 'dastreport',
              				reportFiles: 'dast.html',
              				reportName: 'DAST_Report'
              ]

		    }
	    	}
	  }
	  stage('dast quality gate') {
		    steps {
			    script {
				    def criticaloutput = sh(script: 'cat dastreport/dast.xml | grep -i high | wc -l', returnStdout: true).trim()
				    def criticalnumber = criticaloutput.toInteger()
				    def criticalthreshold = 4
				    if( criticalnumber > criticalthreshold) {
					    error("dast failled, so aborting the build")
				    }
			    }
		    }
	    }
	    stage('VAPT') {
		    steps {
			    script {
				    sh '''
	   				sudo chmod 777 $PWD/vapt
					sudo docker run --rm -v $(pwd)/vapt:/openvas/results/ sathishbob/openvas /openvas/run_scan.py 123.123.123.123 openvas_scan_report -u root -p password'''
			    }
		    }
	  }
    }
    post {
	success {
            emailext body: "Please check console aouput at $BUILD_URL for more information\n", to: "sathishbabudevops@gmail.com", subject: 'Jenkinstraining - $PROJECT_NAME build completed sucessfully - Build number is $BUILD_NUMBER - Build status is $BUILD_STATUS' 
        }  
    }
}
