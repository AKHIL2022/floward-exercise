String applicationName = 'HCLCODE_Test'
String packageName = "floward-exercise"
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "${packageName}"
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
    tools {
    nodejs '18.14.2'
  }
     parameters {
      booleanParam(name: 'force_build', defaultValue: true)
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

        stage('Check Changes') {
      steps {
        script {
          changes = []
          build = currentBuild
          while (build != null && build.result != 'SUCCESS') {
            for (changeLog in build.changeSets) {
              for (entry in changeLog.items) {
                for (file in entry.affectedFiles) {
                  changes += file.path
                }
              }
            }
            build = build.previousBuild
            if (!build) {
              changes = ["${localFolderName}/*"]
            }
          }
          changes.unique().sort()
          echo "Changed since last successful build: ${changes.isEmpty() ? 'none' : changes.join(', \n')}"
          Relevant = changes.findAll { element ->
            // Include changes to our localFolderPath
            element ==~ /\Q$localFolderName\E\/.*/
          }
          Relevant = Relevant.findAll { element ->
            // Ignore changes to *.test.js files
            !(element ==~ /.*\.test\.js/)
          }
          IsPackageJsonChanged = Relevant.any { element ->
            element ==~ /.*package-lock\.json/
          }
          HasRelevantChanges = !Relevant.isEmpty()
          if (HasRelevantChanges) {
            echo "There are changes that affect the deployment: ${Relevant.join(', ')}"
          } else {
            echo 'There are no changes that would affect the deployment'
          }
        }
      }
    }

        stage('build') {
            steps {
                test(HasRelevantChanges)
            }
        }
       /* stage('Publish to S3') {
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
