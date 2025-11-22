pipeline {
    agent none
    tools {
    	maven 'maven-test'
	}
    parameters {
     choice choices: ['prod', 'dev'], name: 'deploy'
    }
    environment {
        DEPLOY_ENV = "${params.deploy}"
    }

    stages {
        stage('Build')
        {   
            agent { label 'slave1'}
            steps{
                echo 'Building Project ...'
                echo "Running on, ${params.deploy}!"
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
            parallel {
                stage('Unit Tests') {
                    agent { label 'slave1'}
                    steps{
                        echo 'Running Unit Tests'
                        sh "mvn test -Dtest=AppTest"
                    }
                }
                stage('Integration Tests') {
                    agent { label 'slave1'}
                    steps{
                        echo 'Running Integration Tests'
                        sh "mvn verify -Dtest=AppIT"
                    }
                }
            }
            post {
                success {
                    dir('webapp/target/') {
                        stash includes: '*.war', name: 'maven-build'
                    }
                }
            }
        }
        stage('Deploy to dev/prod')
        {   
            when{
                environment ignoreCase: true, name: 'DEPLOY_ENV', value: 'dev'
                beforeAgent true
            }
            agent { label 'slave1'}
            steps{
                echo "Deploying to Development Server..."
                dir('/var/www/html/') {
                unstash 'maven-build'
                }
                sh """
                cd /var/www/html/
                java -jar *.war &    
                """
            }
            echo 'Deploying Application to Development Server Successfully!'

            when {
                environment ignoreCase: true, name: 'DEPLOY_ENV', value: 'prod'
                beforeAgent true
            }
            steps {
                echo "Deploy to Production Server ${DEPLOY_ENV} ..."
                agent { label 'slave2'}
                steps {
                dir('/var/www/html/') {
                unstash 'maven-build'
                }
                sh """
                cd /var/www/html/
                java -jar *.war &
                """
                }
            echo 'Deploying Application to Production Server Successfully!'
            }
        }
    }
}
