pipeline {
    agent any
    tools {
        jdk 'JDK21'
        maven 'Maven3'
    }
    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 15, unit: 'MINUTES')
        skipDefaultCheckout(true)
    }
    parameters {
        booleanParam(name: 'RUN_APP', defaultValue: true,
                     description: 'Run the packaged calculator')
    }
    stages {
        stage('Checkout') {
            steps {
                deleteDir()
                checkout scm
            }
        }
        stage('Verify') {
            steps { bat 'call mvn -B clean verify' }
        }
        stage('Run') {
            when { expression { return params.RUN_APP } }
            steps {
                bat 'java -jar target/student-calculator-1.0.0.jar'
            }
        }
    }
    post {
        always {
            junit testResults: 'target/surefire-reports/*.xml',
                  allowEmptyResults: true
        }
        success {
            archiveArtifacts artifacts: 'target/*.jar',
                             fingerprint: true
        }
        failure { echo 'Inspect checkout, compilation and test logs' }
    }
}
