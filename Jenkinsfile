pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        echo 'Preperation for VECTOR Incremental'
        checkout([$class: 'SubversionSCM', 
        additionalCredentials: [], 
        excludedCommitMessages: '', 
        excludedRegions: '', 
        excludedRevprop: '', 
        excludedUsers: '', 
        filterChangelog: false, 
        ignoreDirPropChanges: false, 
        includedRegions: '', 
        locations: [[credentialsId: 'vys-svn', 
                    depthOption: 'infinity', 
                    ignoreExternalsOption: true, 
                    local: 'trunk', 
                    remote: 'svn://vys-svn/vysionics_bsp/trunk']], 
        workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }
    stage('Build') {
      steps {
        echo 'Build VECTOR_defconfig'
        sh 'mkdir vector_incremental_build'
        dir('vector_incremental_build') {
            sh 'mkdir -p target/usr/vysionics/etc/'
            sh 'touch target/usr/vysionics/etc/md5sums.txt'
            sh 'echo "BR2_BUILD_TESTS" >> .config
            sh 'make BR2_JLEVEL=0 BR2_EXTERNAL=../vys_buildroot O=$PWD -C../buildroot/ VECTOR_defconfig'
            sh 'make'
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
      steps {
        echo 'Promote build to QA'
      }
    }
  }
}
