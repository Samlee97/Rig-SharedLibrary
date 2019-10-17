#! /usr/bin/env groovy

@Library('github_connector')_
@Library('Shared_Library_Pipeline@master') __
@Library('sonarqube-quality-gate')___
@Library('nexus-artifact-upload@master') ____
@Library('ArtifactDeployToEC2Library') _______
@Library('shared-libraries-reusable')__________________


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
        
        //git branch: 'Master', credentialsId: 'bitbucket_Url', url: 'http://rig@18.224.68.30:7990/scm/dem/web_3.git'
        git credentialsId: 'bitbucket_Url', url: 'http://rig@${BB_URL}'
   
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
                withSonarQubeEnv('Sonar') {
                sh 'mvn sonar:sonar -Dproject.settings=./sonar.properties'
                }
            }
          }
  
   stage('QualityGate') {       steps {
            echo 'starting quaity gates'          
          qualityGates() //this stage will fail if project quality doesn't meet its quality profile
      }
   }  
         
  
        
         stage("Upload to Nexus") {
             steps {
                  nexus(
                     artifactId:"Web_Build_Artifacts",
                     classifier: "",
                     filePath: "target/JenkinsWar.war",
                     type: "war",
                     credentialsId: "nexus",
                     groupId: "maven-group",
                     nexusUrl: "ec2-18-224-155-110.us-east-2.compute.amazonaws.com:8081/nexus",
                     nexusVersion: "nexus",
                     protocol: "http",
                    repository: "kishan-test",
                    version: "${env.BUILD_NUMBER}"
                  )
            }
        }
       /* stage('AnsibleRoleClone') {
            steps{
                script {
                    deployToEC2.gitClone("http://ec2-3-16-78-26.us-east-2.compute.amazonaws.com/Subinay/jenkinsinstallansiblerole.git")
                    }
                }
        }
        stage('DeployToEC2') {
            steps{
                script {
                    deployToEC2.playbook('jenkinsinstallansiblerole/host', 'jenkinsinstallansiblerole/DeployToEC2.yml')
                }
            }
        }*/
    }
post {
    always {
    notification(currentBuild.currentResult)
}
}
}
