pipeline {
    agent any
    
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MVN3"
    }

    stages {
        stage('Enable webhook') {
            steps {
                script {
                    properties([pipelineTriggers([githubPush()])])
                }
            }
        }
        stage('Pull SCM') {
            steps {
                // Get some code from a GitHub repository
                git credentialsId: 'GitHub', url: 'git@github.com:sathishbob/jenkins_test.git'
            }
        }
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true -f api-gateway clean package"
                // bat "mvn -Dmaven.test.failure.ignore=true -f api-gateway clean package"
            }
            post {
                success {
                    junit 'api-gateway/target/surefire-reports/*.xml'
                    archiveArtifacts 'api-gateway/target/*.jar' 
                    emailext body: "Please check console output at $BUILD_URL for more information \n Artifact is avaliable at $RUN_ARTIFACTS_DISPLAY_URL", to: "sathishbabudevops@gmail.com", subject: '$PROJECT_NAME is completed - Build number is $BUILD_NUMBER - Build ststus is $BUILD_STATUS'
                }
            }
        }
        stage("Email") {
            steps {
                script {
                    cest = TimeZone.getTimeZone("CEST")
                    def cest = new Date()
                    println(cest)
                    def mailRecipients = 'sathishbob@gmail.com'
                    def jobName = currentBuild.fullDisplayName
                    env.Name = jobName
                    env.cest = cest
                    emailext body: '''${SCRIPT, template="email-html.template"}''', mimeType: 'text/html', subject: "${jobName}", to: "${mailRecipients}", replyTo: "${mailRecipients}"
                }
            }
        }
    }
}
