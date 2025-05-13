//@Library('jenkins-shared-library') _

String applicationName = 'HCLCODE_Test'
String localFolderName = 'floward-exercise'
Boolean isPackageJsonChanged = true

pipeline {
    agent any
    tools {
    nodejs '18.14.2'
  }
    stages {
        stage('Check Prerequisites') {
            steps {
                sh '''
                    echo "Checking system prerequisites..."
                    echo "All prerequisites are installed."
                '''
            }
        }

        stage('Mend Scan') {
            steps {
                mend(
                     projectName: "${applicationName}-ui", 
                     folderName: localFolderName, 
                     isPackageJsonChanged:isPackageJsonChanged
                )     
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
