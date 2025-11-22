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
            agent { label 'slave1' }
            steps {
                echo 'Building Project ...'
                echo "Running on, ${params.deploy}!"
                sh 'mvn -B -DskipTests clean package'
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
                    agent { label 'slave1' }
                    steps {
                        sh 'mvn test'
                    }
                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
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
        stage('Deploy to Dev') {
            when {
                beforeAgent true
                environment name: 'DEPLOY_ENV', value: 'dev', ignoreCase: true
            }
            agent { label 'slave1' }

            steps {
                echo "Deploying to Development Server (${DEPLOY_ENV})..."
                dir('/var/www/html/') {
                    unstash 'maven-build'
                }
                sh '''
        cd /var/www/html/
        java -jar *.war &
        '''
                echo 'Deploying Application to Development Server Successfully!'
            }
        }

        stage('Deploy to Prod') {
            when {
                beforeAgent true
                environment name: 'DEPLOY_ENV', value: 'prod', ignoreCase: true
            }
            agent { label 'slave2' }

            steps {
                echo "Deploying to Production Server (${DEPLOY_ENV})..."
                dir('/var/www/html/') {
                    unstash 'maven-build'
                }
                sh '''
        cd /var/www/html/
        java -jar *.war &
        '''
                echo 'Deploying Application to Production Server Successfully!'
            }
        }
    }
}
