#! /usr/bin/env groovy

@Library('Shared-Library16')_


pipeline {
    agent any
    stages{
         
    stage('Create Project in JIRA'){
            steps{
             jira_create_project()
            }
  
 }
 stage('Create Issues in JIRA'){
     steps{
         jira_create_issues()
     }
 }
        stage('Checkout') {
            steps{
       
             //git credentialsId: 'bitbucket_Url', url: 'http://rig@18.224.68.30:7990/scm/dem/app.git'
              bitbucketCheckout()  
   }
            }
        
        stage('Building & Packing')
        { 
            steps{
            building()
            }
        }
    
     stage('Code Analysis') 
           { environment {
             scannerHome=tool 'sonar scanner'
           }
              steps {
                echo 'starting sonarqube anaysis'
                 sonar()
                }
            }
          
  
  stage('QualityGate') {
      steps {
            echo 'starting quaity gates'          
          qualityGates() //this stage will fail if project quality doesn't meet its quality profile
      }
  }  
         
  
        
         stage("Upload to Nexus") {
             steps {
                  Nexus()
                     
             }
            }
         

 stage('AnsibleRoleClone') {
            steps{
                script {
                    deployToEC2.gitClone()
                    }
                }
        }
  stage('DeployToEC2') {
            steps{
                script {
                    deployToEC2.playbook('ansiblerole/host', 'ansiblerole/DeployToEC2.yml')
                  }
            }
        }
    }
/*post {
    always {
    notification(currentBuild.currentResult)
}

}*/
}



