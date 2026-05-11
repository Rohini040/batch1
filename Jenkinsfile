pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        PATH = "/opt/Maven3/bin:$PATH"
    }

    stages {

        stage('SCM Code Clone') {
            steps {
                git branch: 'main',
                    credentialsId: 'GitHub',
                    url: 'https://github.com/Rohini040/batch1.git'
            }
        }

        stage('Code Review') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn clean sonar:sonar'
                }
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Artifact Upload') {
            steps {
                nexusArtifactUploader(
                    artifacts: [[
                        artifactId: 'webapplication',
                        classifier: '',
                        file: 'target/webapplication.war',
                        type: 'war'
                    ]],
                    credentialsId: 'Nexus',
                    groupId: 'com.batch1',
                    nexusUrl: '13.234.38.99:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'maven-snapshots',
                    version: '1.0-SNAPSHOT'
                )
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sshagent(['50a7f6c6-d685-41ff-9eb8-756b22277bf3']) {
                    sh """
                    scp -o StrictHostKeyChecking=no target/webapplication.war ec2-user@172.31.36.5:/opt/tomcat9/webapps/
                    """
                }
            }
        }
    }
}
