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
