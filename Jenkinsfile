pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        echo 'Preperation'
      }
    }
    stage('Build') {
      steps {
        echo 'Build VECTOR_defconfig'
      }
    }
    stage('Test') {
      parallel {
        stage('Unit Test') {
          steps {
            echo 'Unit Tests'
          }
        }
        stage('Static Analysis') {
          steps {
            echo 'Static analysis'
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy to VECTOR(s)'
      }
    }
    stage('Smoke Tests') {
      steps {
        echo 'Smoke tests to verify deployment(s)'
      }
    }
    stage('Integration Tests') {
      steps {
        echo 'Integration tests on target'
      }
    }
    stage('Finalise') {
      steps {
        echo 'Promote build to QA'
      }
    }
  }
}