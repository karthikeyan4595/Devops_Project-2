pipeline{
    agent any
    environment {
      def mvnHome = tool name: 'Maven3', type: 'maven'
      def mvnCMD = "${mvnHome}/bin/mvn"
      def dockertag = "${getDockerTag ()}"
    }
    stages{
        stage("checkout the code from git"){
            steps {
                git 'https://github.com/karthikeyan4595/Devops_Project-2.git'
            }
        }
        stage ("Maven Build") {
            steps {
              sh "${mvnCMD} clean package"
            }
        }
        stage ("Build Docker Image") {
            steps {
                sh "docker build . -t 459512/demo_practice:${dockertag} "
            }
        }
        stage ("Push Docker Image to DockerHub") {
            steps {
                 withCredentials([string(credentialsId: 'DockerHub-Pwd', variable: 'DockerHub')]) {
                    sh "docker login -u 459512 -p ${DockerHub}"
                    sh "docker push 459512/demo_practice:${dockertag}"
                }
            }
        }
        stage ("Ansible Deployment") {
            steps {
                ansiblePlaybook credentialsId: 'deploy-ansible', disableHostKeyChecking: true, extras: "-e dockertag=${dockertag}", installation: 'ansible2', inventory: 'dev.inv', playbook: 'Deployment.yml'
                
            }
        }
    }

    /*
    post {
       success {
          slackSend baseUrl: 'https://hooks.slack.com/services/', 
          channel: '#devops-project', 
          failOnError: true, 
          message: "your Job is Success, Build Started: your Job name is ${env.JOB_NAME} and URL is ${env.BUILD_URL}", 
          tokenCredentialId: 'slack'
        }
       failure {
          slackSend baseUrl: 'https://hooks.slack.com/services/', 
          channel: '#devops-project', 
          failOnError: true, 
          message: "Your Job is Failed, Build Failed: Your Job name is ${env.JOB_NAME} and URL is ${env.BUILD_URL}", 
          tokenCredentialId: 'slack'
        }
    }
    */
}
def getDockerTag () {
   def commitId = sh returnStdout: true, script: 'git rev-parse --short HEAD'
   return commitId
}
