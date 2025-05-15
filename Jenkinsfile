String applicationName = 'HCLCODE_Test'
String packageName = "floward-exercise"
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "${packageName}"
String versionFileName = ""
String authorName = 'Build'
String authorEmail = 'jenkins-test@example.com'
String gitEnvRepoCredentialsId = ''
String gitEnvRepoName = 'floward-exercise'
String gitEnvDevBranchName = 'main'
String gitEnvUrl = "git@github.com:AKHIL2022/${gitEnvRepoName}.git"
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
                 s3ObjectName = publish(
                     applicationName: applicationName,
                     packageName: packageName,
                     s3BucketName: s3BucketName,
                     bundleFileName: bundleFileName
                 )
                }
            }
        }
        stage('test') {
            steps {
                echo "objectname: ${s3ObjectName}"
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
