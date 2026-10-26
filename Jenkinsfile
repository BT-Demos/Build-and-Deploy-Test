
pipeline {
    agent {
        kubernetes {
            defaultContainer 'maven'
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: maven
                image: maven:3.9.10-eclipse-temurin-17-alpine
                command:
                - cat
                tty: true
            """
        }
    }
    environment {
        // You can set environment variables here
        MAVEN_OPTS = "-Dmaven.test.failure.ignore=true"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
                sleep 3
            }
        }
         stage('Test') {
            steps {
                sh 'mvn clean test'
            }
        }
        stage('Publish Test Results') {
            steps {
                junit 'target/surefire-reports/*.xml'
            }
        }
        stage('Registering build artifact') {
            steps {
                script {
                    echo 'Registering the metadata...'
                    def artifactId = registerBuildArtifactMetadata(
                        name: "My TestApp",
                        version: "1.0.0",
                        type: "docker",
                        url: "http://localhost:1112",
                        digest: "13518864707039346163693937",
                    )
                    echo "Artifact Id is: ${artifactId}"
                    env.ARTIFACT_ID = artifactId
                    sleep 3
                }
            }
        }
        stage('Register sample security scan') {
            steps {
                checkout scm
                registerSecurityScan(artifacts: 'scandata/trivy.sarif')
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying.....'
                registerDeployedArtifactMetadata(
                    artifactId: "${env.ARTIFACT_ID}",
                    targetEnvironment: "demo-staging",
                    labels: "stable"
                )
            }
        }
    }
}
