def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    tools {
        maven "MAVEN3.9"
        jdk "JDK17"
    }
    stages {
        stage('Fetch code') {
            steps {
                git branch: 'atom', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving it...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage('UNIT TEST') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Checkstyle Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('CODE ANALYSIS with SONARQUBE') {
            environment {
                scannerHome = tool 'sonar6.2'
                JAVA_OPTS = '-XX:+EnableDynamicAgentLoading --add-opens java.base/java.lang=ALL-UNNAMED --add-opens java.base/java.util=ALL-UNNAMED --add-opens java.base/java.io=ALL-UNNAMED --add-opens java.base/java.math=ALL-UNNAMED'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh """
                        export SONAR_SCANNER_OPTS="${JAVA_OPTS}"
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=vprofile \
                        -Dsonar.projectName=vprofile-repo \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    """
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage("UploadArtifact") {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '192.168.56.21:8081',
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: 'vprofile-repo',
                    credentialsId: 'nexuslogin',
                    artifacts: [
                        [artifactId: 'vproapp',
                         classifier: '',
                         file: 'target/vprofile-v2.war',
                         type: 'war']
                    ]
                )
            }
        }
    }
  post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#devops-vidhya-ci',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
