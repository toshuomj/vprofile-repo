pipeline {
    agent {
        docker {
            image 'maven:3.9.6-eclipse-temurin-17-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
        stage('Fetch Code') {
            steps {
//                git branch: 'vp-rem', url: 'https://github.com/devopshydclub/vprofile-repo.git'
                git branch: 'master', url: 'https://github.com/toshuomj/vprofile-repo.git'
            }
        }
        stage('BUILD') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage('TEST') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit(testResults: 'target/surefire-reports/*.xml', allowEmptyResults : true)
                }
            }
        }
        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
//            post {
//                always {
//                    recordIssues enabledForFailure: true, tool: checkStyle()
//                }
//            }
        }
        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  //nexusUrl: '172.31.39.229:8081',
                  nexusUrl: 'adt-nexus.easyvista-training.com:8081',
                  groupId: 'QA',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: 'adt-mjose',
                  credentialsId: 'adt-nexus-mjose',
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
}
