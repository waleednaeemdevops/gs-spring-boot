pipeline {
    agent any
    environment {





      PROJECT_ID = "jenkins-pipeline-364010"
        CLUSTER_NAME = "autopilot-cluster-1"
        LOCATION = "us-central1"
        CREDENTIALS_ID = "jenkins-pipeline"
    }
    tools{
        maven 'mvn'
    }
    stages {
        stage('Build ') {
            steps {
                echo 'Building Maven Project ...'
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'e7e10879-fa4a-437d-aaf0-d48db05975aa', url: 'https://github.com/waleednaeemdevops/gs-spring-boot.git']]])
                /*sh 'mvn -Dmaven.test.skip clean package'*/
                dir("/var/lib/jenkins/workspace/cicd-waleed/complete") {
                sh "echo ==================="
                sh "pwd"
                sh "echo ==================="
                //sh 'mvn -DskipTests package -f ./complete/pom.xml package'
                sh 'mvn -DskipTests package'
}
                sh "echo ********************"
                sh "pwd"
                sh "echo ********************"
           //       sh 'cd complete'
            //    sh 'mvn -DskipTests package'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing Spring Boot Project ...'
                dir("/var/lib/jenkins/workspace/cicd-waleed/complete") {
                sh 'mvn test'
                }
            }
        }
        stage('Build and push Image to GCR') {
            steps {
                echo 'Building Docker Image ..'
                //    sh  "export GOOGLE_CLOUD_PROJECT=`gcloud config list --format="value(core.project)"`"
                dir("/var/lib/jenkins/workspace/cicd-waleed/complete") {
                sh "mvn -DskipTests com.google.cloud.tools:jib-maven-plugin:build -Dimage=gcr.io/jenkins-pipeline-364010/hello-java:v1"
                }
            }
        }
        
    
stage('Deploy To GKE') {
     steps {
         echo "Deployment started ..."
         sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
          step([$class: 'KubernetesEngineBuilder', \
           projectId: env.PROJECT_ID, \
           clusterName: env.CLUSTER_NAME, \
           location: env.LOCATION, \
           manifestPattern: 'deployment.yaml', \
           credentialsId: env.CREDENTIALS_ID, \
           verifyDeployments: true])
     }
}
}
}
