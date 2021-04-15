pipeline{
    environment 
    {
        registry1 = "jarrayaahmed99/hariapp"
        registryCredential = 'dockerpassword'
        img=''
     }
    agent any
    tools {
      maven 'maven3'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
             git url:'https://github.com/JarrayaAhmed99/goMyCodeLabProject.git', branch:'master'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                script {
                    
                 img= docker.build(registry1 , "-f ${env.WORKSPACE}/Dockerfile .")
                       }
              
            }
        }
        
        stage('DockerHub Push'){
            steps{
                script 
                     {
                         docker.withRegistry( '', registryCredential) {
                          img.push(env.BUILD_ID)
                          img.push("latest")
                     }
                }
                
               
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
