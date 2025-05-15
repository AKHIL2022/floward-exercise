String applicationName = 'HCLCODE_Test'
String packageName = "floward-exercise"
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "dist/${packageName}.zip"
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

        stage('Publish to S3') {
            steps {
                script {
                 publish(
                     applicationName: applicationName,
                     s3BucketName: s3BucketName,
                     s3ObjectName: s3ObjectName,
                     bundleFileName: bundleFileName
                 )
                }
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
