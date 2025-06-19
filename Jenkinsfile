String applicationName = 'HCLCODE_Test'
String localFolderName = ""
String packagename = ""
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "${packagename}"
String versionFileName = "src/ui-zip.auto.tf"
String authorName = 'Build'
String authorEmail = 'jenkins-test@example.com'
String gitEnvRepoCredentialsId = 'github-jenkins'
String gitEnvRepoName = 'floward-exercise'
String gitEnvDevBranchName = 'main'
String gitEnvUrl = "git@github.com:AKHIL2022/${gitEnvRepoName}.git"
String gitCredentialId = 'github-jenkins'
boolean isPackageJsonChanged
boolean hasRelevantChanges

pipeline {
    agent any
    tools { nodejs '18.14.2' }
     parameters {
      booleanParam(name: 'forceBuild', defaultValue: false)
  }
    options{
      //buildDiscarder(logRotator(numToKeepStr: '5'))
      disableConcurrentBuilds(abortPrevious: true)
      disableResume()
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
         stage('Install') {
      steps {
        dir(localFolderName) {
          withCredentials([sshUserPrivateKey(credentialsId: gitCredentialId, keyFileVariable: 'SSH_KEY')]) {
            sh "GIT_SSH_COMMAND=\"ssh -i \\\"$SSH_KEY\\\"\""
          }
        }
      }
    }
        stage('Check Changes') {
      steps {
        script {
            (isPackageJsonChanged, hasRelevantChanges) = checkChanges(localFolderName)
            echo "Parsed isPackageJsonChanged: ${isPackageJsonChanged}"
            echo "Parsed hasRelevantChanges: ${hasRelevantChanges}"
      }
    }
}
    stage('Publish') {
       when {
            expression {
               return hasRelevantChanges || params.forceBuild
             }
          }
      steps {
        script {
           s3ObjectName = publish(
            applicationName,
            packagename,
            bundleFileName,
            s3BucketName
           )
            echo "Parsed isPackageJsonChanged: ${isPackageJsonChanged}"
            echo "Parsed hasRelevantChanges: ${hasRelevantChanges}"
        }
      }
    }
         /*stage('Update Dev') {
            when {
                expression {
                   return hasRelevantChanges || params.forceBuild
                }
            }
            steps {
                update(gitEnvRepoCredentialsId, gitEnvDevBranchName, gitEnvUrl, gitEnvRepoName, versionFileName,
                    packagename, s3ObjectName, applicationName)
            }
        }*/

        /*stage('Mend Scan') {
            steps {
                script{
                 def projectName = "${applicationName}-ui"
                mend(
                     projectName,
                     isPackageJsonChanged,
                     forceBuild
                )
              }
            }
        }*/
    }
}
