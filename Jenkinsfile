pipeline {
           agent {
                label 'windows'
        }
    
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MVN3"
    }

    stages {
        stage('pull scm') {
            steps {
                // Get some code from a GitHub repository
                git credentialsId: 'GITHUB', url: 'git@github.com:vishnupriya195/jenkins_test.git'
            }
        }
        
        stage('Build') {
            steps {
                bat "mvn -Dmaven.test.failure.ignore=true -f api-gateway/ clean package"
            }
                            
        }
        
        stage('archive') {
            steps {
                archiveArtifacts artifacts: 'api-gateway/target/*.jar', followSymlinks: false
            }
        }
        
        stage('publish test result') {
            steps {
                junit 'api-gateway/target/surefire-reports/*.xml'
            }
        }
        stage('test')
        {
            
        steps{
            bat "echo testing"
        }
    }
}
}
