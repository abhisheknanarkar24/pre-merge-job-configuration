pipeline {
    agent any
    environment{
        GIT_REVISION_NUMBER= """${sh(
                returnStdout: true,
                script: 'git rev-parse HEAD'
            )}"""
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '${sha1}']], extensions: [], userRemoteConfigs: [[credentialsId: 'GitHub', name: 'origin', refspec: '+refs/pull/*:refs/remotes/origin/pr/*', url: 'https://github.com/abhisheknanarkar24/pre-merge-job-configuration.git']]])
            }
        }
        stage('SonarQube Analysis') {
            
            steps{
                withSonarQubeEnv(installationName: 'SonarQube', credentialsId: 'SonarQubeJenkinsIntegration') {
                    sh "/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/sonar-scanner-4.6.2.2472-linux/bin/sonar-scanner"
                  }  
                  }
            }
        stage("Quality Gate") {
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }       
    }
}
    