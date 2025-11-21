pipline {
    agent none
    stages {
        stage('Build')
        {   
            agent { label 'slave1'}
            steps{
                echo 'Building Project'
                mvn -B -DskipTests clean package
            }
        }
        stage('Test')
        {
            steps{
                echo 'Running Tests'
                mvn test
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
