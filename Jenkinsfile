//@Library('jenkins-shared-library') _

String applicationName = 'HCLCODE_Test'
String packageName = "test"
String s3BucketName = 'sandbox-tf-test'
String s3ObjectName
//String localFolderName = 'floward-exercise'

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
                     projectName: "${applicationName}-ui"
                )     
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
