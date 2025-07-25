String applicationName = 'HCLCODE_Test'
String localFolderName = "noob"
String lamdaName = "HCLCODE_Test-deployment-10"
String s3BucketName = 'tf-test-1'
String s3ObjectName
String bundleFileName = "${lamdaName}"
String versionFileName = "ui-zip.auto.tf"
String authorName = 'Build'
String authorEmail = 'jenkins-test@example.com'
String gitEnvRepoCredentialsId = 'github-jenkins'
String gitEnvRepoName = 'floward-exercise-deployment-97'
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
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        disableConcurrentBuilds(abortPrevious: true)
        disableResume()
    }
    environment{
        DYNAMODB_LOCAL_PORT = 8000
        DYNAMODB_LOCAL_DIR = "${localFolderName}/dynamodb_local"
        COVERAGE_THRESHOLD = 70
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
                install(gitCredentialId)
            }
        }
        /*stage('Npm Audit') {
            when {
                expression {
                    return hasRelevantChanges || params.forceBuild
                }
            }
            steps {
                script {
                    sh 'npm audit --audit-level=high'
                }
            }
        }*/
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
                    lamdaName, s3ObjectName, applicationName, localFolderName, localsFormat = 'lamdaBuild')
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
