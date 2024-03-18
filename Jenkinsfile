pipeline {
  agent none
  stages {
    stage('Build') {
      failFast true
      parallel {
        stage('Build1.17') {
          agent {
            node {
              label 'go-1.17'
} }
          steps {
            echo 'Compiling and building'
            echo "Building on ${NODE_NAME}"
            sh 'go build -o output/demoapp'
            } }
        stage('Build1.16') {
          agent {
            node {
              label 'go-1.16'
} }
          steps {
            echo "Building on ${NODE_NAME}"
} }
} }
} tools {
go 'go' }
  environment {
    GO114MODULE = 'on'
    CGO_ENABLED = 0
    GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    PKG_PATH = '/home/jenkins/pkgs'
} }
