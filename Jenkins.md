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
Jenkinsfile attributes are the building blocks that define the pipeline’s behavior, execution environment, logic, and post-processing.
1. pipeline
The top-level block that defines the entire pipeline.
2. agent
Specifies where the pipeline or a specific stage will run (e.g., any, none, or a specific label).
3. parameters
Defines input parameters that can be provided when starting the pipeline (e.g., string, boolean, choice).
4. environment
Sets environment variables that are available to all stages or specific stages.
5. options
Configures various pipeline-level options (e.g., timeouts, build discarding, concurrency).
6. triggers
Defines how and when the pipeline should be triggered (e.g., by SCM polling, cron).
7. stages
Contains a sequence of stage blocks, which group steps into logical units.
8. stage
A block within stages that defines a named phase of the pipeline (e.g., Build, Test, Deploy).
9. steps
The actual commands to run in a stage (e.g., sh, echo, script, bat).
10. tools
Defines tools to automatically install and use (e.g., JDK, Maven).
11. post
Defines actions that occur after pipeline or stage execution, based on result (always, success, failure, etc.).
12. when
Conditional execution for stages or steps (e.g., run only on certain branches or parameters).
13. input
Used to pause the pipeline and wait for human input.

Example:


pipeline {
    agent any
    parameters {
        string(name: 'BRANCH', defaultValue: 'main', description: 'Branch to build')
    }
    environment {
        VAR = 'value'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
    }
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
    post {
        success {
            echo 'Build successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
