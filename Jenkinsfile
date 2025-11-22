pipeline {
    agent none
    tools {
    	maven 'maven-test'
	}
    stages {
        stage('Build')
        {   
            agent { label 'slave1'}
            steps{
                echo 'Building Project'
                sh "mvn -B -DskipTests clean package"
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
        stage('Test')
        {
	    agent { label 'slave1'}
            steps{
                echo 'Running Tests'
                sh "mvn test"
            }
        }
        stage('Deploy')
        {   
            agent { label 'slave1'}
            steps{
                echo 'Deploying Application'
            }
        }
    }
}
