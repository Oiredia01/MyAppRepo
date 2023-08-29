pipeline {
  agent any
 
  tools {
  maven 'Maven3'
  }
    stages {
    
        stage ("Checkout") {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '2f8c4694-f302-4034-822d-4b0d10e7b6fe', url: 'https://github.com/Oiredia01/MyAppRepo']])
            }
        }
        stage ('Build') {
            steps {
            sh 'mvn clean install -f MyWebApp/pom.xml'
      }
    }
        stage ('Code Quality') {
            steps {
            withSonarQubeEnv('SonarQube') {
            sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
        }
      }
    }
        stage ('Code Coverage') {
            steps {
            jacoco()
      }
    }
        stage ('Nexus Upload') {
            steps {
            nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: 'c447f797-b22c-4bfc-9d5d-9d16fbab0b79', groupId: 'com.dept.app', nexusUrl: 'ec2-18-224-171-224.us-east-2.compute.amazonaws.com:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
      }
    }
        stage ('DEV Deploy') {
            steps {
            echo "deploying to DEV Env "
            deploy adapters: [tomcat9(credentialsId: '0c635483-ef39-4050-bdba-f29b48c09292', path: '', url: 'http://ec2-3-16-68-118.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
      }
    }
        stage ('Slack Notification') {
            steps {
            echo "deployed to DEV Env successfully"
            slackSend(channel:'august-automate-job', message: "Deployed to DEV env successfully")
      }
    }
        stage ('DEV Approve') {
            steps {
            echo "Taking approval from DEV Manager for QA Deployment"
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to deploy?', submitter: 'admin'
        }
      }
    }
        stage ('QA Deploy') {
            steps {
            echo "deploying to QA Env "
            deploy adapters: [tomcat9(credentialsId: '0c635483-ef39-4050-bdba-f29b48c09292', path: '', url: 'http://ec2-3-16-68-118.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
        }
    }
        stage ('QA Approve') {
            steps {
            echo "Taking approval from QA manager"
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to proceed to PROD?', submitter: 'admin,manager_userid'
        }
      }
    }
        stage ('Slack Notification for QA Deploy') {
            steps {
            echo "deployed to QA Env successfully"
            slackSend(channel:'august-automate-job', message: "Deployed to QA env successfully")
      }
    }  
  }
}
