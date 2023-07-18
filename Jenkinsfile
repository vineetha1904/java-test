pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = "us-east-1"
        
        // function_name = 'java-sample'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn package'
            }
        }
        
        stage('sonarqube analysis') {
            when {
                anyOf {
                    branch 'feature/*'
                    branch 'main'
                }
            }
            steps {
                withSonarQubeEnv('Sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
            post {
                always {
                    script {
                        try {
                            // Add your desired step or command here
                            echo 'Quality Gate succeeded'
                        } catch (Exception e) {
                            // Handle any exceptions
                            echo "Quality Gate failed: ${e.getMessage()}"
                        }
                    }
                }
            }
        }
        
        stage('Deployment') {
            parallel {
                stage('Test') {
                    steps {
                        echo 'Deployment to Test'
                    }
                }
            
                stage('UAT Deployment') {
                    steps {
                        echo 'Deployment to UAT'
                    }
                }
            }
        }
        
        stage('Push') {
            when {
                anyOf {
                    branch 'main'
                }
            }
            steps {
                echo 'Push'
                
            }
        }
    }
}
