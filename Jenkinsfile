pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MVN3"
    }

    stages {
        stage('pull') {
            steps {
                // Get some code from a GitHub repository
                git credentialsId: 'git', url: 'git@github.com:sathishbob/jenkins_test.git'
                }
        }
        
        stage('build') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true -f api-gateway/ clean package"
            }
        }

        stage('publish') {
            steps {
                junit stdioRetention: '', testResults: 'api-gateway/target/surefire-reports/*.xml'
                archiveArtifacts artifacts: 'api-gateway/target/*.jar', followSymlinks: false
            }
        }

        stage('print') {
            steps {
                sh "echo testing"
            }
        }
    }
}
