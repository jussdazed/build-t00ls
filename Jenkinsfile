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
            sh "${tool("sonar")}/bin/sonar-scanner -Dsonar.projectKey=final -Dsonar.projectName=final "
          }
        }

      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 2, unit: 'MINUTES') {
          waitForQualityGate true
        }

      }
    }

    stage('Testing') {
      steps {
        dir(path: 'helloworld-project/helloworld-ws/') {
          sh 'mvn test'
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
}