pipeline {
  agent none
  stages {
    stage('Pre-build') {
      agent {
        node {
          label 'jdk21'
        }
      }
      environment {
        GOPATH = "${PKG_PATH}"
      }
      steps {
        echo 'Installing dependencies'
        sh 'go install golang.org/x/lint/golint@latest'
      }
    }
    stage('Build') {
      failFast true
      parallel {
        stage('Build1.21.8') {
          agent {
            node {
              label 'go-1.21.8'
            }
          }
          
          steps {
            echo 'Compiling and building'
            echo "Building on ${NODE_NAME}"
            sh 'go build -o output/demoapp'
          }
          post {
            success {
              stash(name: 'my-app', includes: 'output/demoapp')
            }
          }
        }
        stage('Build1.16') {
          agent {
            node {
              label 'go-1.21.8'
            }
          }
          steps {
            echo "Building on ${NODE_NAME}"
          }
        }
      }
    }
    stage('Test') {
      agent {
        node {
          label 'jdk21'
        }
      }
      steps {
        withEnv(overrides: ["PATH+GO=${PKG_PATH}/bin"]) {
          echo 'Running vetting'
          sh 'go vet .'
          echo 'Running linting'
          sh 'golint .'
          echo 'Running test'
          sh 'go test -v'
        }
      }
    }
    stage('Confirm Deploy') {
      when {
        branch 'deploy'
      }
      steps {
        timeout(time: 60, unit: 'SECONDS') {
          input(message: 'Okay to Deploy?', ok: 'Let\'s Do it!')
        }
      }
    }
    stage('Deploy') {
      agent {
        node {
          label 'jdk17'
        }
      }
      when {
        branch 'deploy'
      }
      steps {
        unstash 'my-app'
        sh './output/demoapp'
      }
    }
  }
  tools {
    go 'go'
  }
  environment {
    GO111MODULE = 'auto'
    CGO_ENABLED = 0
    GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    PKG_PATH = '/home/jenkins/pkgs'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
    preserveStashes(buildCount: 2)
  }
}
