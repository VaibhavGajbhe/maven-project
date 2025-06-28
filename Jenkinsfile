pipeline {
    agent {
        label 'DevServer'
    }
    tools {
    maven 'myMaven'
   }

    stages {
        stage('Build') {
            steps {
                // Build the project using Maven
                sh 'mvn clean package'
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
    }
}
