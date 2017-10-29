pipeline {
  agent any
  stages {
    stage('Preperation') {
      steps {
        echo 'Preperation'
      }
    }
    stage('Checkout') {
      steps {
        echo 'Checkout vysionics_bsp'
      }
    }
    stage('Build') {
      parallel {
        stage('VECTOR') {
          steps {
            echo 'Build VECTOR_defconfig'
          }
        }
        stage('VECTOR SR') {
          steps {
            echo 'Build VECTOR_SR_defconfig'
          }
        }
        stage('VECTOR SPECS') {
          steps {
            echo 'Build VECTOR SPECS'
          }
        }
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
      parallel {
        stage('Promotion') {
          steps {
            echo 'Promote build to QA'
          }
        }
        stage('Archive') {
          steps {
            echo 'Archive build outputs'
          }
        }
      }
    }
  }
}