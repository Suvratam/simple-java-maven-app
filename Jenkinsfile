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
            agent { label 'slave2'}
            steps{
                echo 'Deploying Application'
            }
        }
    }
}
