pipeline {
    agent any
    
    parameters {
        string(name: 'MY_PARAM', defaultValue: 'Hello xinh cuộc sống đẹp xinh', description: 'Enter a value for MY_PARAM')
        string(name: 'BRANCH', defaultValue: 'master', description: 'Enter a value for MY_PARAM')
      
    }

    environment {
        MY_ENV_VAR = "${params.BRANCH}"
        VENV = "/Users/test/Working/Jenkins/workspace/python-jobs/python-pipeline/venv"
        ALLURE_RESULTS_DIR = 'allure-results'
        ALLURE_REPORT_DIR = 'allure-report'
    }

    options {
            // Keep the last 5 builds and delete older builds
            buildDiscarder(logRotator(numToKeepStr: '5'))
        }

    stages {
        
        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                brew install allure
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    // Activate the virtual environment and run the tests
                    sh '''
                    source venv/bin/activate
                    pytest test_python.py
                    '''
                }
            }
        }
        stage('Generate Allure Report') {
            steps {
                sh '''
                allure generate ${ALLURE_RESULTS_DIR} -o ${ALLURE_REPORT_DIR} --clean
                '''
            }
        }
        stage('Archive Allure Report') {
            steps {
                archiveArtifacts artifacts: "${ALLURE_REPORT_DIR}/**", fingerprint: true
            }
        }
    }
     post {
        always {
            script {
                allure([
                    reportBuildPolicy: 'ALWAYS',
                    results: [[path: "${ALLURE_RESULTS_DIR}"]]
                ])
            }
        }
        cleanup {
            cleanWs() // Clean the workspace after the build
        }
    }
}
