pipeline {
    agent any
    stages {
        stage('Unit Test') {
            steps {
                
                    sh 'gradle clean test'
                
            }
            post {
                always {
                    junit 'app/build/test-results/testDebugUnitTest/*.xml'
                }
            }
        }
        stage('Static Code Analysis') {
            steps {
                sh 'gradle clean lint'
            }
            post {
                always {
                    androidLint canComputeNew: false, defaultEncoding: '', healthy: '', pattern: 'app/build/reports/lint-results.xml', unHealthy: ''
                }
            }
        }
        stage('Build') {
            steps {
                sh 'gradle build -x test -x lint'
            }
        }
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('local') {
                    sh '/usr/local/bin/mvn clean package -Dsonar.projectKey=demo1 -Dsonar.projectName=demo1 -Dsonar.projectDescription="New Project for Demo" sonar:sonar'
                    } // submitted SonarQube taskId is automatically attached to the pipeline context
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        //a55a1961526e5c8ec9570a79d1d01cfbcd808358
        stage('Publish to App Center') {
            environment {
                APPCENTER_API_TOKEN = credentials('appcenter-api-token')
            }
            steps {
                appCenter apiToken: a55a1961526e5c8ec9570a79d1d01cfbcd808358,
            ownerName: 'utstulsy-publicis',
            appName: 'android-test',
            pathToApp: 'app/build/outputs/apk/release/app-release-unsigned.apk',
            distributionGroups: 'Collaborators, internal',
            buildVersion: "${env.BUILD_ID}"
            }
        }
    }
}
