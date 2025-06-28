pipeline {

    agent {
      label 'DevServer'
    }

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
                // Build the project using Maven
                sh 'mvn clean package -DskipTests=true'
                // echo "Hello ${NAME} ${params.LASTNAME}"
            }
            
        }
        stage('Test') {
            parallel {
              stage(testA){
                steps {
                    agent {label 'DevServer'}
                    echo "Running tests for A"
                    sh 'mvn test'
                }
              }
              stage(testB){
                steps {
                    agent {label 'DevServer'}
                    echo "Running tests for B"
                    sh 'mvn test'
                }
              }
            }
            post {
                success {
                  dir ("test-pipeline/target") {
                    stash name : "maven-build", includes: "**/target/*.war"
                    // archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
    }
    stage('Deploy_dev') 
    {
        when { expression { params.select_environemnt == 'dev' } 
        beforeAgent true }
        agents {
            label 'DevServer'
        }
        steps
        {
             dir ("/var/www/html")
             {
                unstash 'maven-build'
                sh 'cp target/*.war .'
                sh 'systemctl restart tomcat'
                echo "Deployed to Dev Server"
             }
             sh """
             cd /var/www/html/
             jar -xvf *.war
             """
        }
            
    }
 }
}