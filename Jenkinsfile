node {
    def mvnHome = tool 'Maven3'

    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        sh "${mvnHome}/bin/mvn clean install -f MyAwesomeApp/pom.xml"
    }

    stage('Nexus Repository Staging') {
        withNexusPublisher(nexusVersion: 'nexus3', nexusUrl: 'http://54.235.233.1:8081', credentialsId: '366da518-c536-48ab-8c16-43abd32a8e63', protocol: 'http') {
            nexusArtifactUploader(
                nexusInstanceId: 'nexus',
                protocol: 'http',
                nexusRepositoryId: 'maven-snapshots',
                groupId: 'myGroupId',
                version: '1.0-SNAPSHOT',
                repositoryPath: '',
                files: 'MyAwesomeApp/target/*.war'
            )
        }
    }

    stage('DEV Deploy') {
        echo "Deploying to DEV Env"
        deploy adapters: [tomcat8(credentialsId: '53402dea-6f4f-406f-a31e-0d928fe4f0f1', path: '', url: 'http://54.205.190.5:8080')], contextPath: null, war: '**/*.war'
    }

    stage('DEV Approve') {
        echo "Taking approval from DEV"
        timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to deploy?', submitter: 'admin'
        }
    }

    stage('Slack Notification') {
        slackSend(channel: 'myawesomeapp', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
}
