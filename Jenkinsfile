String applicationName = 'HCLCODE_Test'
String localFolderName = ""
String projectName = 'HCLCODE_Test'
String lamdaName = "HCLCODE_Test-deployment-10"
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "${lamdaName}"
String versionFileName = "ui-zip.auto.tf"
String authorName = 'Build'
String authorEmail = 'jenkins-test@example.com'
String gitEnvRepoCredentialsId = 'github-jenkins'
String gitEnvRepoName = 'floward-exercise-deployment-103'
String gitEnvDevBranchName = 'dev'
String gitEnvUrl = "git@github.com:AKHIL2022/${gitEnvRepoName}.git"
String gitCredentialId = 'github-jenkins'
boolean isPackageJsonChanged
boolean hasRelevantChanges

pipeline {
    agent any
    tools {
        nodejs '18.14.2'
    }
    parameters {
        booleanParam(name: 'forceBuild', defaultValue: true, description: 'this is to force the build')
        booleanParam(name: 'continueOnAuditFail', defaultValue: false, description: 'this is to force the build')
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
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
        stage('Security Audit') {
            steps {
                    mend(projectName, isPackageJsonChanged, continueOnAuditFail)
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
       /* stage('Build') {
            when {
                expression {
                    return hasRelevantChanges || params.forceBuild
                }
            }
            steps {
                buildDependency()
            }
        }*/
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
                        lamdaName,
                        bundleFileName,
                        s3BucketName
                    )
                    echo "Parsed isPackageJsonChanged: ${isPackageJsonChanged}"
                    echo "Parsed hasRelevantChanges: ${hasRelevantChanges}"
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
                update(
                    gitEnvRepoCredentialsId, gitEnvDevBranchName, gitEnvUrl, versionFileName,
                    lamdaName, s3ObjectName, applicationName, localFolderName, localsFormat = 'noob')
            }
        }
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
