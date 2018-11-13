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
    always { 
      httpRequest authentication: 'githubfjbot', httpMode: 'POST', consoleLogResponseBody: true, requestBody: """{
                 "body": "Nice change!\n Build log: ${RUN_DISPLAY_URL}\n Build url: http:\/\/${VIRTUAL_HOST}\/"
           }""", responseHandle: 'STRING', url: "https://api.github.com/repos/fjbot/testrepo/issues/${CHANGE_ID}/comments"
    }
  }
}
