pipeline {
  agent any
  stages {
    stage('Git') {
      steps {
        git(branch: 'main', url: 'https://github.com/jussdazed/build-t00ls.git')
      }
    }

    stage('Building Code') {
      steps {
        dir(path: 'helloworld-project/helloworld-ws/') {
          sh 'mvn package -DskipTests'
        }

      }
    }

    stage('Scan') {
      steps {
        script {
          def scannerHome = tool 'sonar';
          withSonarQubeEnv('sonar') {
            sh "${tool("sonar")}/bin/sonar-scanner \
            -Dsonar.host.url=http://192.168.49.1:9000/ \
            -Dsonar.projectKey=final \
            -Dsonar.projectName=final \
            -Dsonar.sources=helloworld-project/helloworld-ws/src/main/java/org/jboss/as/quickstarts/wshelloworld/ \
            -Dsonar.tests=helloworld-project/helloworld-ws/src/test/java/org/jboss/as/quickstarts/wshelloworld/"
          }
        }

      }
    }

    stage('Quality Gate') {
      steps {
        script {
          def qualitygate = waitForQualityGate()
          sleep(10)
          if (qualitygate.status != "OK") {
            waitForQualityGate abortPipeline: true
          }
        }

      }
    }

    stage('Testing') {
      parallel {
        stage('pre-integration-test') {
          steps {
            dir(path: 'helloworld-project/helloworld-ws/') {
              sh 'mvn pre-integration-test '
            }

          }
        }

        stage('integration-test') {
          steps {
            dir(path: 'helloworld-project/helloworld-ws/') {
              sh 'echo mvn integration-test '
            }

          }
        }

        stage('post-integration-test') {
          steps {
            dir(path: 'helloworld-project/helloworld-ws/') {
              sh 'echo mvn post-integration-test'
            }

          }
        }

      }
    }

    stage('Asking for manual approval') {
      steps {
        input(message: 'Approve the deployment', ok: 'yes')
      }
    }

  }
  tools {
    maven 'mvn'
  }
  environment {
    registryCredentials = 'nexus'
    registry = 'http://192.168.49.1:8081'
    sonarCredentials = 'sonar'
  }
}