node {
   def mvnHome
   def server=Artifactory.server "giri_art"
    

       stage('Preparation') {
           
         git "https://github.com/gireeshgg/gun.git"
         mvnHome=tool 'Maven'
        }
        stage('SonarQube'){
          withSonarQubeEnv('Sonar')  {
             // withEnv( ["PATH+MAVEN=${mvnHome}/bin"] ) {
                 
                sh "mvn clean package sonar:sonar"
           // }
          }
        }
         stage("Quality Gate") {
            //timeout(time: 30, unit: 'SECONDS') { // Just in case something goes wrong, pipeline will be killed after a timeout
            def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
            if (qg.status != 'OK') {
            error "Pipeline aborted due to quality gate failure: ${qg.status}"
            //}
            }
         }
  
       stage('build'){
       
             withEnv( ["PATH+MAVEN=${mvnHome}/bin"] ) {
             sh "mvn clean package"
            }
         
        }
        
        stage('Artifactory upload') {
        def uploadSpec = """
        { "files": [ { "pattern": "/var/lib/jenkins/workspace/scriptypipe/target/gspring.war", "target": "girish" } ] }"""
            server.upload(uploadSpec)
        }
        stage('downloading artifact')
        {
        def downloadSpec="""{ "files":[ { "pattern":"girish/gspring.war", "target":"/var/lib/jenkins/workspace/" } ] }"""
        server.download(downloadSpec)
        } 
        
    
}
