pipeline {
    agent any
    
    tools {
        maven "maven3.9.6"
    }

    stages {
        stage("Git clone") {
            steps {
                git branch: 'main', credentialsId: 'tomcat-credentials', url: 'https://github.com/kessiey/web-app-kessie.git'
            }
        }

        stage("Build with Maven") {
            steps {
                sh "mvn clean"
            }
        }

        stage("Testing with Maven") {
            steps {
                sh "mvn test"
            }
        }

        stage("Package with Maven") {
            steps {
                sh "mvn package"
            }
        }

        stage('SonarQube Analysis') {
            environment {
                ScannerHome = tool 'sonar5.0'
            }
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        sh "${ScannerHome}/bin/sonar-scanner -Dsonar.projectKey=jomacs"
                    }
                }
            }
        }

                stage("Upload to Nexus") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'maven-web-application', 
                classifier: '', 
                file: '/var/lib/jenkins/workspace/first-pipeline-job/target/web-app.war', 
                type: 'war']], 
                credentialsId: 'nexus-id', 
                groupId: 'com.mt', 
                nexusUrl: '3.12.111.23:8081/', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'webapp-release', 
                version: '3.0.6-RELEASE'
            }
        }

                stage("Deploy to UAT") {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', 
                path: '', 
                url: 'http://3.139.82.80:8080/')], 
                contextPath: null, 
                war: 'target/*.war'
            }
        }
    }
}
