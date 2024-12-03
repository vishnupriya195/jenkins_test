pipeline {
    agent any
    
    tools {
        maven "MVN3"
    }
    
    stages {
        stage('pull scm') {
            steps {
                git credentialsId: 'github', url: 'git@github.com:sathishbob/jenkins_test.git'
            }
        }
        
        stage('build') {
            steps {
                sh "mvn -f api-gateway/ clean package"
            }
        }
        
        stage('publish') {
            steps {
                junit stdioRetention: '', testResults: 'api-gateway/target/surefire-reports/*.xml'
                archiveArtifacts 'api-gateway/target/*.jar'
            }
        }

        stage('print') {
            steps {
                echo "testing"
            }
        }
    }
}
