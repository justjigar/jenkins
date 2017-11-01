pipeline {
  agent any
  parameters {
      choice(
          choices: 'incremental\nclean',
          description: 'Build type',
          name: 'BUILD_ACTION')
  }
  stages {
    stage('Checkout') {
      steps {
        script {
          if( params.BUILD_ACTION == 'clean' ) {
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
                          local: 'vysionics_bsp', 
                          remote: 'svn://vys-svn/vysionics_bsp/trunk']], 
              workspaceUpdater: [$class: 'UpdateUpdater']])        
          }
          else {
            dir('./vysionics_bsp/vector_incremental_build/build/vysionics-HEAD') {
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
                          local: './', 
                          remote: 'svn://vys-svn/specs_3_vector/Software/trunk']], 
              workspaceUpdater: [$class: 'UpdateUpdater']])
              sh 'rm .stamp_configured .stamp_built .stamp_staging_installed .stamp_target_installed'
            }
          }
        }
      }
    }
    stage('Build') {
      steps {
        script {
          if( params.BUILD_ACTION == 'clean' ) {
            echo 'Build VECTOR_defconfig'
            wrap([$class: 'TimestamperBuildWrapper']) {
              sh 'mkdir -p ./vysionics_bsp/vector_incremental_build'
              dir('./vysionics_bsp/vector_incremental_build') {
                  sh 'mkdir -p target/usr/vysionics/etc/'
                  sh 'touch target/usr/vysionics/etc/md5sums.txt'
                  sh 'make BR2_JLEVEL=0 BR2_EXTERNAL=../vys_buildroot O=$PWD -C../buildroot/ VECTOR_defconfig'
                  sh 'make BR2_BUILD_TESTS=y'
                  sh 'make'
              }
            }
          }
          else {
            echo 'Build Incremental of vysionics-HEAD'
            wrap([$class: 'TimestamperBuildWrapper']) {
              dir('./vysionics_bsp/vector_incremental_build') {
                  sh 'make'            
              }
            }
          }
        }
      }
    }
    stage('Test') {
      parallel {
        stage('Unit Test') {
          steps {
            dir('./vysionics_bsp/vector_incremental_build/build/vysionics-HEAD/buildroot-build/') {
              dir('./bofservice/src/bofservice-build'){
                sh 'LD_LIBRARY_PATH=$WORKSPACE/build/install/usr/vysionics/lib/:$WORKSPACE/build/boost/src/boost_1_58_0/stage/lib/:$WORKSPACE/build/install/opencv_2_4_11/lib/ ./src/test/test_bofservice --gtest_output=xml:test_bofservice.xml'
              }
            }
            step([$class: 'XUnitPublisher', testTimeMargin: '3000', thresholdMode: 1, thresholds: [[$class: 'FailedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: ''], [$class: 'SkippedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: '']], tools: [[$class: 'GoogleTestType', deleteOutputFiles: false, failIfNotNew: true, pattern: '**/src/*-build/test_*.xml', skipNoTestFiles: false, stopProcessingIfError: true]]])
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
