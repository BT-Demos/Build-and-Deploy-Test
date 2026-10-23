
pipeline {
    agent any
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
                        version: "1.0.1",
                        type: "docker",
                        url: "http://localhost:1112",
                        digest: "93518064707039346163693937",
                        label: "bug-bash"
                    )
                    echo "Artifact Id is: ${artifactId}"
                    env.ARTIFACT_ID = artifactId
                    sleep 3
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying.....'
                registerDeployedArtifactMetadata(
                    artifactId: "${env.ARTIFACT_ID}",
                    targetEnvironment: "CBCI",
                    labels: "CBCI"
                )
            }
        }
        stage('Deploy2') {
            steps {
                echo 'Deploying...'
                registerDeployedArtifactMetadata(
                    artifactId: "${env.ARTIFACT_ID}",
                    targetEnvironment: "Aruna_Test",
                    labels: "Aruna_Test"
                )
            }
        }
    }
}
