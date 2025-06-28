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
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
    }
}