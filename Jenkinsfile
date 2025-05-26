String applicationName = 'HCLCODE_Test'
String Lambdaname = "floward-exercise-test"
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "${Lambdaname}"
String versionFileName = "src/ui-zip.auto.tf"
String authorName = 'Build'
String authorEmail = 'jenkins-test@example.com'
String gitEnvRepoCredentialsId = 'github-jenkins'
String gitEnvRepoName = 'floward-exercise-deployment'
String gitEnvDevBranchName = 'dev'
String gitEnvUrl = "git@github.com:AKHIL2022/${gitEnvRepoName}.git"
String localFolderName = 'floward-exercise'

pipeline {
    agent any
     parameters {
      booleanParam(name: 'force_build', defaultValue: false)
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


        /*stage('build') {
            steps {
                test(
                        force_build: params.force_build,
                        localFolder: localFolderName)
            }
        }*/
       stage('Publish to S3') {
            steps {
                script {
                 s3ObjectName = publish(
                     applicationName: applicationName,
                     Lambdaname: Lambdaname,
                     s3BucketName: s3BucketName,
                     bundleFileName: bundleFileName
                 )
                }
            }
        }
        /*stage('test') {
            steps {
                update(
                     packageName: packageName,
                     s3BucketName: s3BucketName,
                     gitEnvDevBranchName: gitEnvDevBranchName,
                     versionFileName: versionFileName,  
                     authorName: authorName,
                     authorEmail: authorEmail,
                     gitEnvRepoCredentialsId: gitEnvRepoCredentialsId,
                     s3ObjectName: s3ObjectName,
                     gitEnvUrl: gitEnvUrl,
                     gitEnvRepoName: gitEnvRepoName
                    
              )
            }
        }
        stage('Mend Scan') {
            steps {
                mend(
                     projectName: "${applicationName}-ui"
                )     
            }
        }*/
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
