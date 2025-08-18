#Jenkins notes:

First Example:
---------------------

Jenkins pipeline:
=====================

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-org/your-selenium-project.git', branch: 'main'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'mvn clean install -U -DskipTests -s settings.xml -Djavax.net.ssl.trustStore=nexus.jks -Djavax.net.ssl.trustStorePassword=changeit'
            }
        }
        stage('Run Selenium Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Archive Results') {
            when {
                expression {
                    // Only run this stage if all previous stages were successful
                    currentBuild.currentResult == 'SUCCESS'
                }
            }
            steps {
                junit '**/target/surefire-reports/*.xml'
                archiveArtifacts artifacts: '**/target/*.html', allowEmptyArchive: true
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}



