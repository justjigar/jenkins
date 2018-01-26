pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        script {
          if( params.BUILD_ACTION == 'clean' ) {
            echo 'Preperation for VECTOR Clean build'
            sh 'rm -rf vysionics_bsp'
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
            dir('./vysionics_bsp/vector_incremental_build') {
              sh 'rm -f images/*.xz'
            }
          }
        }
        
      }
    }
    stage('Python Integration Tests') {
      environment {
        PYTHONPATH = "$WORKSPACE/vysionics_bsp/vector_incremental_build/build/vysionics-HEAD/inttest/packages/"
      }
      steps {
        echo 'Integration tests on target'
        dir(path: './vysionics_bsp/vector_incremental_build/build/vysionics-HEAD/inttest/py_tests/') {
          sh 'py.test -vs test_ftp.py --junit-xml=xml/test_ftp.xml'
          sh 'py.test -vs test_utmc12.py --junit-xml=xml/test_utmc12.xml'
        }
        
      }
      post {
        always {
          step([$class: 'XUnitPublisher', testTimeMargin: '3000', thresholdMode: 1, thresholds: [[$class: 'FailedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: ''], [$class: 'SkippedThreshold', failureNewThreshold: '', failureThreshold: '', unstableNewThreshold: '', unstableThreshold: '']], tools: [[$class: 'GoogleTestType', deleteOutputFiles: false, failIfNotNew: true, pattern: 'vysionics_bsp/vector_incremental_build/build/vysionics-HEAD/inttest/py_tests/xml/*.xml', skipNoTestFiles: false, stopProcessingIfError: true]]])
          
        }
        
      }
    }
    stage('Finalise') {
      steps {
        echo 'Promote build to QA'
      }
    }
  }
  parameters {
    choice(choices: '''incremental
clean''', description: 'Build type', name: 'BUILD_ACTION')
  }
}