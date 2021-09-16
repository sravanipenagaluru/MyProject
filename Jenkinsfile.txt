pipeline {
    agent any 
    tools {
        maven 'Maven'
    }

    stages {
        stage('gitcheckout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'git_credentials', url: 'https://github.com/sravanipenagaluru/MyProject.git']]])
            }
        }
        
        stage('build') {
            steps {
                bat 'mvn clean install'
            }
        }
        
        stage('codeQuality') {
            steps {
                bat 'mvn sonar:sonar'
            }
        }
        
        stage('Artifact') {
            steps {
                bat 'mvn deploy -P release'
            }
        }
        
        stage('deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: '69c8656b-3fc4-48dc-beb2-085f452c4039', path: '', url: 'http://localhost:4343/')], contextPath: 'MyProject', war: '**/*.war'
            }
        }
    }
}
