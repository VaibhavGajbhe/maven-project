pipeline {

    agent {
        label 'DevServer'
    }

    parameters {
    string defaultValue: 'SUCHDEVA', name: 'LASTNAME'
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
                sh 'mvn clean package'
                echo "Hello ${NAME} ${params.LASTNAME}"
            }
            
        }
        stage('Test') {
            parallel {
              stage(testA){
                steps {
                    echo "Running tests for A"
                }
              }
              stage(testB){
                steps {
                    echo "Running tests for B"
                }
              }
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
    }
    
}