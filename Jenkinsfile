pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install -f MyAwesomeApp/pom.xml'
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
                    nexusUrl: '3.82.93.21:8081',
                    groupId: 'myGroupId',
                    version: '1.0-SNAPSHOT',
                    repository: 'maven-snapshots',
                    credentialsId: '75f5be44-b099-4816-91dc-dbc163e8b420',
                    artifacts: [
                        [
                            artifactId: 'MyAwesomeApp',
                            classifier: '',
                            file: 'MyAwesomeApp/target/MyAwesomeApp.war',
                            type: 'war'
                        ]
                    ]
                )
            }
        }

        stage('DEV Deploy') {
            steps {
                echo "Deploying to DEV Env"
                deploy adapters: [tomcat9(credentialsId: 'b0a1ab36-6a31-40ae-9844-93bffac638ee', path: '', url: 'http://44.220.141.143:8080/')], contextPath: null, war: '**/*.war'
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
