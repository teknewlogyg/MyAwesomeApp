pipeline {
    agent any
    
    tools {
        maven 'Maven3'
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install -f MyWebApp/pom.xml'
            }
        }
        
        stage('JaCoCo') {
            steps {
                jacoco()
            }
        }
        
        stage('Nexus Upload') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'http://54.235.233.1:8081',
                    groupId: 'myGroupId',
                    version: '1.0-SNAPSHOT',
                    repository: 'maven-snapshots',
                    credentialsId: '366da518-c536-48ab-8c16-43abd32a8e63',
                    artifacts: [
                        [
                            artifactId: 'MyAwesomeApp',
                            classifier: '',
                            file: 'MyAwesomeApp/target/MyAwesomeeApp.war',
                            type: 'war'
                        ]
                    ]
                )
            }
        }
        
        stage('DEV Deploy') {
            steps {
                echo "Deploying to DEV Env"
                deploy adapters: [tomcat9(credentialsId: '53402dea-6f4f-406f-a31e-0d928fe4f0f1', path: '', url: 'http://54.205.190.5:8080')], contextPath: null, war: '**/*.war'
            }
        }
        
        stage('Slack Notification') {
            steps {
                echo "Deployed to DEV Env successfully"
                slackSend(channel:'myawesomeapp', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
    }
}
