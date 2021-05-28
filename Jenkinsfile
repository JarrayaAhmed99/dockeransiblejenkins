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
        
        stage('notify')
        {
            steps {
                 mail to: 'ahmed.jarraya99@gmail.com',               
                    subject: "Job $JOB_NAME failed" ,
                    body: """Build $BUILD_NUMBER failed.    
Go to $BUILD_URL for more info."""
                //emailext (
            //subject: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
            //body: """<p>STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
              //<p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
            //recipientProviders: [[$class: 'DevelopersRecipientProvider']]
         // )

//emailext body: 'this is a notification  email sent from Jenkins', subject: 'Jenkins notification', to: 'jarayaahmed@gmail.com'     
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
                sh "mvn sonar:sonar"
              }
        }
       }
            
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
        
        stage('Pushing images to Dockerhub'){
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
        stage('notify2')
        {
            steps {

emailext body: 'this is test', subject: 'test', to: 'ahmed.jarraya99@gmail.com'     
            }
        }
        
        
        stage('ssh to an ansible server and execute playbooks for deployment ') {
            steps {
                sshCommand remote: remote, command: "ls -al"
                sshCommand remote: remote, command: "ansible-playbook play-deployment.yml -i inventory.txt"
                sshCommand remote: remote, command: "ansible-playbook play-service.yml -i inventory.txt"
            }
        }
        
       
        
       
       
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
