node{
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')), parameters([booleanParam(defaultValue: false, description: 'Need to upload artifact?', name: 'upload_artifact'), booleanParam(defaultValue: false, description: 'Need to download artifact?', name: 'download_artifact')]), pipelineTriggers([])])
    deleteDir()
    
    stage ('clone'){
        
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/arrthy/Maven-project.git']]])
         }
         
     stage ('maven build'){
         bat "mvn clean install"
    }
    
    if( params.upload_artifact )
    {
    stage ('upload to artifactory'){
        def server = Artifactory.server "artifactory"
        def uploadSpec =
            '''{
            "files": [
                {
                    "pattern": "target/*.jar",
                    "target": "release-project1-local"
               }
            ]
        }'''
        server.upload(uploadSpec)
        
    }
    }
	if( params.download_artifact )
    {
    stage ('download from artifactory'){
        def server =  Artifactory.server "artifactory"
        def downloadSpec =
        '''{
            "files": [
            {
                "pattern": "release-project1-local/simple-1.0-SNAPSHOT.jar",
                "target": "simple-1.0-SNAPSHOT.jar"
            }
            
            ]
            
        }'''
        server.download(downloadSpec)
    }
	}
	
	stage ('verify')
	{
	    def file = fileExists 'simple-1.0-SNAPSHOT.jar'
	    if ( file )
	    {
	        print "jar downloaded from artifactory"
	    }
	    
	    else
	    {
	        print "jar not downloaded from artifactory"
	    }
	}
}
