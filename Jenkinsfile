pipeline {

  agent any
    options {
      timeout(time: 1, unit: 'DAYS')
       disableConcurrentBuilds()
    }

  stages {
    stage('Checkout Version Control') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh "mvnw package"
        }
      }

    stage('SonarQube Code Scan') {
      environment {
        SCANNER_HOME = tool 'sonarqube-scanner'
        ORGANIZATION = "Administrator"
        PROJECT_NAME = "flask-app-python"
      }
      steps {
        withSonarQubeEnv('sonar-qube') {
            sh "$SCANNER_HOME/bin/sonar-scanner \
            -Dsonar.java.binaries=build/classes/java/ \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.sources=."
        }
      }
    }

    stage("SonarQube Quality Gate") {
      steps {
        waitForQualityGate abortPipeline: true
        }
      }

  post {
    success {
        setBuildStatus("Build succeeded", "SUCCESS");
        archiveArtifacts 'target/*.jar'
    }
    failure {
        setBuildStatus("Build failed", "FAILURE");
    }
  }
}

void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/petrus-fatu/ci-cd"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}
