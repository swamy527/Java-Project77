pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    environment {
                 SCANNER_HOME = tool 'son-scanner'
       } 
    stages {
        stage('checkout') {
            steps {
                sh 'ls'
            }
        }
        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('unit-test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('file system scan') {
            steps {
                sh 'trivy fs --format table -o fs.html .'
            }
        }
        stage('sonar-analysis') {
            steps {
               withSonarQubeEnv('sonar') {
                sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=TaskMaster -Dsonar.projectName=TaskMaster -Dsonar.java.binaries=.'
               }
            }
        }
        stage('builing application') {
            steps {
                sh 'mvn package'
            }
        }
        stage('publish artifact') {
            steps {
                withMaven(globalMavenSettingsConfig: 'nexus-config', jdk: '', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                     sh 'mvn deploy'
                }
            }
        }
        stage('build image') {
            steps {
                sh 'docker build -t dockerswaha/taskmaster:latest .'
            }
        }
        stage('image scan') {
            steps {
                sh 'trivy image --format table -o image.html dockerswaha/taskmaster:latest'
            }
        }
    }
}