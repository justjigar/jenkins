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
                          local: './ipf', 
                          remote: 'svn://vys-svn/ipf/trunk']], 
              workspaceUpdater: [$class: 'UpdateUpdater']])
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
                          local: './libVysUtils', 
                          remote: 'svn://vys-svn/vysionics_common/trunk/libVysUtils']], 
              workspaceUpdater: [$class: 'UpdateUpdater']])
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
                          local: './libVysCrypt', 
                          remote: 'svn://vys-svn/vysionics_common/trunk/libVysCrypt']], 
              workspaceUpdater: [$class: 'UpdateUpdater']])
              sh 'rm -f .stamp_configured .stamp_built .stamp_staging_installed .stamp_target_installed'
              sh 'rm -rf buildroot-build'
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
          environment { 
            LD_LIBRARY_PATH = "$WORKSPACE/vysionics_bsp/vector_incremental_build/target/usr/vysionics/lib/:$WORKSPACE/vysionics_bsp/vector_incremental_build/target/usr/lib/"
          }
          steps {
            dir('./vysionics_bsp/vector_incremental_build/build/vysionics-HEAD/buildroot-build/') {
              dir('./aspd/src/aspd-build'){
                sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./src/test/test_aspd --gtest_output=xml:test_aspd.xml'
              }
              dir('./bofservice/src/bofservice-build'){
                sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./src/test/test_bofservice --gtest_output=xml:test_bofservice.xml'
              }
              // dir('./commissioning/src/commissioning-build'){
              //  sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./src/test/test_commissioning --gtest_output=xml:test_commissioning.xml'
              //  step([$class: 'XUnitPublisher', testTimeMargin: '3000', thresholdMode: 1, thresholds: [[$class: 'FailedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: ''], [$class: 'SkippedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: '']], tools: [[$class: 'GoogleTestType', deleteOutputFiles: false, failIfNotNew: true, pattern: '**/src/*-build/test_*.xml', skipNoTestFiles: false, stopProcessingIfError: true]]])
              //}
              dir('./libVysUtils/src/libVysUtils-build'){
                sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./src/test/test_VysUtils --gtest_output=xml:test_VysUtils.xml'
              }
              //dir('./libengine/src/libengine-build'){
              //  sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./src/test/test_libengine --gtest_output=xml:test_libengine.xml'
              //}
              //dir('./libengine_qfree/src/libengine_qfree-build'){
              //  sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./src/test/test_libengine_qfree --gtest_output=xml:test_libengine_qfree.xml'
              //}
              dir('./nrsd/src/nrsd-build'){
                sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./src/test/test_nrsd --gtest_output=xml:test_nrsd.xml'
              }
              dir('./supervisor/src/supervisor-build'){
                sh 'LD_LIBRARY_PATH=${LD_LIBRARY_PATH} ./test/test_supervisor --gtest_output=xml:test_supervisor.xml'
              }
            }
          }
          post {
            always {
                step([$class: 'XUnitPublisher', testTimeMargin: '3000', thresholdMode: 1, thresholds: [[$class: 'FailedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: ''], [$class: 'SkippedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: '']], tools: [[$class: 'GoogleTestType', deleteOutputFiles: false, failIfNotNew: true, pattern: '**/src/*-build/test_*.xml', skipNoTestFiles: false, stopProcessingIfError: true]]])
            }
          }
        }
        stage('Static Analysis') {
          steps {
            echo 'Static analysis'
          }
        }
      }
    }
    stage('Package & Deploy') {
      steps {
        echo 'Package and Deploy to VECTOR(s)'
        wrap([$class: 'TimestamperBuildWrapper']) {
          dir('./vysionics_bsp/vector_incremental_build') {
              echo 'Call ./installer.sh'            
          }
        }
      }
      post {
        always {
            archive './vysionics_bsp/vector_incremental_build/images/*bzImage'
            archive './vysionics_bsp/vector_incremental_build/images/*rootfs.cpio.xz'
        }
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
