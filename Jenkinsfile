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
        dir("target") {
          stash name: "maven-build", includes: "maven-web-application.war"
        }
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
      post {
        success {
          dir("test-pipeline/target") {
            stash name: "maven-build", includes: "**/*.war"
            // archiveArtifacts artifacts: '**/target/*.war'
          }
        }
      }
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
        }
        sh '''
          cd /var/www/html/
          jar -xvf *.war
        '''
      }
    }
  }
}
