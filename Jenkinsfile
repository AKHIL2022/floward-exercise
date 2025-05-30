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
     stage('Publish') {
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
            s3BucketName,
            bundleFileName
           )
        }
      }
    }
        stage('build') {
             when {
               expression {
               return HasRelevantChanges || params.force_build
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
                mend(
                     projectName: "${applicationName}-ui",
                     IsPackageJsonChanged: IsPackageJsonChanged,
                     force_build: params.force_build
                )     
            }
        }
    }
}
