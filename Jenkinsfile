pipeline {
  agent { label 'DevServer' }

  parameters {
    choice choices: ['dev', 'prod'], name: 'select_environemnt'
  }

  tools {
    maven 'myMaven'
  }

  environment {
    NAME = 'Vaibhav'
  }

  stages {
    stage('Build') {
      steps {
        script{
                file = load "script.groovy"
                file.hello()
        }
        sh 'mvn clean package -DskipTests=true'
        sh 'find target -name "*.war"'
        stash name: 'maven-build', includes: 'target/maven-web-application.war'
        archiveArtifacts artifacts: 'target/*.war'
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
      
    }

    stage('Deploy_dev') {
      when {
        expression { params.select_environemnt == 'dev' }
        beforeAgent true
      }
      agent { label 'DevServer' }
      steps {
         // Use a directory Jenkins can access
    dir("${env.WORKSPACE}/deploy") {
        unstash 'maven-build'
        sh 'cp target/*.war .'
        sh 'jar -xvf *.war'
        sh 'sudo cp -r * /var/www/html/'
        sh 'sudo systemctl restart tomcat'

        echo "Deployed to Dev Server"
    }
      }
    }
    stage('deploy_prod')
    {
      when { expression {params.select_environment == 'prod'}
        beforeAgent true}
        agent { label 'ProdServer' }
        steps
        {
             timeout(time:5, unit:'DAYS'){
                input message: 'Deployment approved?'
             }
            dir("/var/www/html")
            {
                unstash "maven-build"
            }
            sh """
            cd /var/www/html/
            jar -xvf *.war
            """
        }  
    }
  }
}
