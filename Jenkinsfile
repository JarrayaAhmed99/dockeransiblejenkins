def remote = [name: 'ansible', host: '192.168.183.7', user: 'ahmed', password: '24149100', allowAnyHosts: true]
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
    //    stage('doing some analysiss using sonarqube')
      //  {
        //    steps {     
            
          //      withSonarQubeEnv('sonar6') { 
         // sh "mvn sonar:sonar"
           //     }
        //}
            
           //stage('Quality Gate') {
           //   steps {
          //        timeout(time: 1, unit: 'HOURS') {
            //      waitForQualityGate abortPipeline: true
           //        }
            //   }
          //}
            
            
            
            
       // }
        //stage('mail khfif')
        //{
       // steps
         //   {
           // mail bcc: 'ahmed.jarraya99@gmail.com', body: 'everything is working till now', cc: '', from: '', replyTo: '', subject: 'state', to: 'ahmed.jarraya99@gmail.com'
            //}
        //}
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
        stage('notify')
        {
            steps {

emailext body: 'this is test', subject: 'test', to: 'ahmed.jarraya99@gmail.com'     
            }
        }
        
        
        stage('SSH Declarative Example') {
            steps {
                sshCommand remote: remote, command: "ls -al"
                sshCommand remote: remote, command: "ansible-playbook play-deployment.yml -i inventory.txt"
                sshCommand remote: remote, command: "ansible-playbook play-service.yml -i inventory.txt"
            }
        }
        
       
        
        stage ('where am i')
         {
             steps {
                 sh 'hostname'
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
