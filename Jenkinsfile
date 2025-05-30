String applicationName = 'HCLCODE_Test'
//String Lambdaname = "floward-exercise-test"
String packagename = "floward-exercise-test"
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "${packagename}"
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
    tools { nodejs '18.14.2' }
     parameters {
      booleanParam(name: 'forceBuild', defaultValue: false)
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
            def returnValue = checkChanges(localFolderName)
            isPackageJsonChanged = returnValue.isPackageJsonChanged
            hasRelevantChanges = returnValue.hasRelevantChanges
      }
    }
}
    /* stage('Publish') {
       when {
            expression {
               return HasRelevantChanges || params.force_build
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
    }*/
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
