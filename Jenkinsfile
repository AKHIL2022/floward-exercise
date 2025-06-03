String applicationName = 'HCLCODE_Test'
//String Lambdaname = "floward-exercise-test"
String packagename = "floward-exercise"
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
String localFolderName = ""

pipeline {
    agent any
    tools { nodejs '18.14.2' }
     parameters {
      booleanParam(name: 'forceBuild', defaultValue: false)
  }
    environment {
        // Define localFolderName in the environment block
        localFolderName = "${env.WORKSPACE ?: sh(script: 'pwd', returnStdout: true).trim()}"
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
            def returnValues = checkChanges(localFolderName)
            isPackageJsonChanged = returnValues[0]
            hasRelevantChanges = returnValues[1] 
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
        }
      }
    }
         stage('Update Dev') {
            when {
                expression {
                   return hasRelevantChanges || params.forceBuild
                }
            }
            steps {
                update(packagename, s3BucketName, gitEnvDevBranchName, versionFileName, authorName,
                    authorEmail, gitEnvRepoCredentialsId, s3ObjectName, gitEnvUrl, gitEnvRepoName)
            }
        }
        stage('build') {
             when {
               expression {
               return hasRelevantChanges || params.forceBuild
             }
          }
      steps {
        dir(localFolderName) {
          test()
        }
      }
    }

        stage('Mend Scan') {
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
        }
    }
}
