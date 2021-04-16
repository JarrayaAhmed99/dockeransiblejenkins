pipeline{
    environment 
    {
        registry1 = "jarrayaahmed99/hariapp"
        registryCredential = 'dockerpassword'
        img=''
        DOCKER_TAG = getVersion()
     }
    agent any
    tools {
      maven 'maven3'
    }
    
    stages{
        stage('SCM'){
            steps{
             git url:'https://github.com/JarrayaAhmed99/dockeransiblejenkins.git', branch:'master'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        stage('doing some analysiss using sonarqube')
        {
            steps {     
            
                withSonarQubeEnv('sonar6') { 
          sh "${mvnHome}/bin/mvn sonar:sonar"
                }
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
