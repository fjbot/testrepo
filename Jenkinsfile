import groovy.json.*

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
      script {
        def body = """Successfully built!
        --------------
        
        | Environment   | Link          |
        | ------------- | ------------- |
        | Build         | ${BUILD_URL}  |
        
        --------------
        Refer to this link for build results (access rights to CI server needed): 
        ${RUN_DISPLAY_URL}"""
        def jsonbody = JsonOutput.toJson([body: body])
        def url = "https://api.github.com/repos/fjbot/testrepo/issues/${CHANGE_ID}/comments"
        def response = httpRequest authentication: 'githubfjbot', httpMode: 'POST', consoleLogResponseBody: true, requestBody: jsonbody, responseHandle: 'STRING', url: url
        if (response.status != 201) {
          error("Failed creating comment: " + response.status)
        }
      }
    }
    failure { 
      httpRequest authentication: 'githubfjbot', httpMode: 'POST', consoleLogResponseBody: true, requestBody: """{
                 "body": "Failure!\\n--------------\\nRefer to this link for details: ${RUN_DISPLAY_URL}"
           }""", responseHandle: 'STRING', url: "https://api.github.com/repos/fjbot/testrepo/issues/${CHANGE_ID}/comments"
    }
  }
}
