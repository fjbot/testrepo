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
        def body="""{
                 "body": "Nice change",
                 "commit_id": "${GIT_COMMIT}",
                 "path": "/",
                 "position": 0
           }"""
        def response = httpRequest authentication: 'githubfjbot', httpMode: 'POST', requestBody: body, responseHandle: 'STRING', url: 'https://api.github.com/repos/fjbot/testrepo/issues/${CHANGE_ID}/comments'
        if(response.status != 201) {
            error("Commenting Failed: " + response.status)
        }
      }
    }
    stage('link') {
      steps {
        echo 'http://' + VIRTUAL_HOST + '/'
      }
    }
  }
}
