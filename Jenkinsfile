pipeline {
  agent any
  stages {
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
