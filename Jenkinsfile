pipeline {
  agent {
    node {
      label 'slave'
      customWorkspace 'builds/test/test-' + env.BRANCH_NAME  + '-' + env.BUILD_NUMBER
    }
  }

  environment {
    SKIP_DB_DATA_VOLUME_UPDATE = 1
    DB_DATA_VOLUME = "/home/jslave/dumps/test-latest.tar.gz"
    VIRTUAL_HOST = "${BUILD_NUMBER}.${BRANCH_NAME.toLowerCase()}.ygbw.${VIRTUAL_HOST_BASE}"
    BUILD_URL = "http://${VIRTUAL_HOST}/"
  }

  stages {
    stage('build') {
      steps {
        sh 'env'
      }
    }
    stage('link') {
      steps {
        echo 'http://' + VIRTUAL_HOST + '/'
      }
    }
  }
  post { 
    success { 
      httpRequest authentication: 'githubfjbot', httpMode: 'POST', consoleLogResponseBody: true, requestBody: """{
                 "body": "Successfully built!\\n--------------\\nBuild log: ${BUILD_URL}\\n--------------\\nRefer to this link for details: ${RUN_DISPLAY_URL}"
           }""", responseHandle: 'STRING', url: "https://api.github.com/repos/fjbot/testrepo/issues/${CHANGE_ID}/comments"
    }
    failure { 
      httpRequest authentication: 'githubfjbot', httpMode: 'POST', consoleLogResponseBody: true, requestBody: """{
                 "body": "Failure!\\n--------------\\nRefer to this link for details: ${RUN_DISPLAY_URL}"
           }""", responseHandle: 'STRING', url: "https://api.github.com/repos/fjbot/testrepo/issues/${CHANGE_ID}/comments"
    }
  }
}
