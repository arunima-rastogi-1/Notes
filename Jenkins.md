pipeline {
    agent any

    parameters {
        string(name: 'GIT_REPO_URL', defaultValue: 'https://github.com/your-org/your-selenium-project.git', description: 'Git repository URL')
        string(name: 'GIT_BRANCH', defaultValue: 'main', description: 'Branch to checkout')
        string(name: 'JMETER_HOME', defaultValue: '/opt/apache-jmeter-5.6.3', description: 'Path to JMeter Home')
        string(name: 'JMETER_SCRIPT', defaultValue: 'tests/sample_test.jmx', description: 'Path to JMeter script')
        string(name: 'RESULTS_DIR', defaultValue: 'results', description: 'Results directory')
        string(name: 'MVN_SETTINGS', defaultValue: 'settings.xml', description: 'Maven settings file')
        string(name: 'MVN_TRUSTSTORE', defaultValue: 'nexus.jks', description: 'TrustStore for Maven')
        string(name: 'MVN_TRUSTSTORE_PASSWORD', defaultValue: 'changeit', description: 'TrustStore password')
    }

    environment {
        JAVA_HOME   = "${params.JMETER_HOME}"         // Update path as needed
        MAVEN_HOME  = "${params.JMETER_SCRIPT}"       // Path to your JMeter script
        RESULTS_DIR = "${params.RESULTS_DIR}"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: params.GIT_REPO_URL, branch: params.GIT_BRANCH
            }
        }
        stage('Install Dependencies') {
            steps {
                sh """
                    mvn clean install -U -DskipTests \
                    -s ${params.MVN_SETTINGS} \
                    -Djavax.net.ssl.trustStore=${params.MVN_TRUSTSTORE} \
                    -Djavax.net.ssl.trustStorePassword=${params.MVN_TRUSTSTORE_PASSWORD}
                """
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

------------ Conditional stage execution ---------------

pipeline {
    agent any

    parameters {
        booleanParam(name: 'RUN_DEPLOY', defaultValue: false, description: 'Run the Deploy stage?')
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            when {
                expression {
                    params.RUN_DEPLOY == true
                }
            }
            steps {
                echo 'Deploying...'
            }
        }
    }
}

---------------
