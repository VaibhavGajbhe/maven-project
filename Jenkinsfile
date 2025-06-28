pipeline {
  agent { label 'DevServer' }

  parameters {
    choice choices: ['dev', 'prod'], name: 'select_environemnt'
  }

  tools {
    maven 'myMaven'
  }

  environment {
    NAME = 'piyush'
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn clean package -DskipTests=true'
        sh 'find target -name "*.war"'
        stash name: 'maven-build', includes: 'target/maven-web-application.war'
        archiveArtifacts artifacts: 'target/*.war', fingerprint: true
      }
    }

    stage('Test') {
      parallel {
        stage('testA') {
          agent { label 'DevServer' }
          steps {
            echo "Running tests for A"
            sh 'mvn test'
          }
        }
        stage('testB') {
          agent { label 'DevServer' }
          steps {
            echo "Running tests for B"
            sh 'mvn test'
          }
        }
      }
      // ❌ Removed the broken post { success { stash ... } } block
    }

    stage('Deploy_dev') {
      when {
        expression { params.select_environemnt == 'dev' }
        beforeAgent true
      }
      agent { label 'DevServer' }
      steps {
        dir("/var/www/html") {
          unstash 'maven-build'
          sh 'cp target/*.war .'
          sh 'systemctl restart tomcat'
          echo "Deployed to Dev Server"
          sh 'jar -xvf *.war'
        }
      }
    }
  }
}
