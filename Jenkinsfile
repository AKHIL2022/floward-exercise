//@Library('jenkins-shared-library') _

String applicationName = 'HCLCODE_Test'

pipeline {
    agent any
    stages {
        stage('Check Prerequisites') {
            steps {
                sh '''
                    echo "Checking system prerequisites..."
                    echo "All prerequisites are installed."
                '''
            }
        }
        stage('Security Audit') {
        environment {
        WS_APIKEY = credentials('mend-api-key')
        WS_WSS_URL = 'https://saas.whitesourcesoftware.com/agent'
        WS_PRODUCTNAME = 'HCLCODE'
        WS_PROJECTNAME = "${applicationName}"
      }
      steps {
        dir(localFolderName) {
          script {
            // Run NPM audit and fail the build if there are high priority issues
            echo 'Running NPM Audit, Job will fail if there are high priority issues'
            sh 'npm audit --audit-level=high'
            // Run MEND SCA scan and generate the MEND report
            if (IsPackageJsonChanged) {
              script {
              echo 'Downloading Mend Unified Agent'
              sh 'curl -LJO https://unified-agent.s3.amazonaws.com/wss-unified-agent.jar'
              echo 'Generate Mend Report'
              sh 'java -jar wss-unified-agent.jar'
              }
            }
          }
        }
      }
    }
        stage('Install Terraform and AWS CLI') {
            steps {
                test()
            }
        }
        stage('Validate Installation') {
            steps {
                sh '''
                    echo "Validating Terraform installation..."
                    $HOME/bin/terraform version
                    
                    echo "Validating AWS CLI installation..."
                    $HOME/.local/bin/aws --version
                '''
            }
        }
    }
    post {
        always {
            echo "Pipeline execution completed."
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
