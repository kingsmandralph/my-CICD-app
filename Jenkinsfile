pipeline{
    agent any
    parameters {
	  choice choices: ['UAT', 'PERFORMACE', 'PROD'], description: 'Target environment', name: 'TARGET_ENV'
    }
	tools {
        maven 'maven-3.6.3'
        jdk 'jdk8'
    }
    stages{
        stage('init'){
            steps{
                script{
                    cleanWs()
                    println("Preparing job with variables")
                    println("deploying in ${TARGET_ENV}")
                    currentBuild.displayName = "#${BUILD_NUMBER} Started by ${currentBuild.getBuildCauses()[0].userId}"
                    currentBuild.description = """Target Env is: ${TARGET_ENV} """
                }
            }
        }
        stage('download jar from s3') {
            steps{
		script{                   
                    withAWS(credentials: '318867684519', region: 'eu-west-2') {
                        sh '''echo "Downloading jar from s3 for deployment to ${ENV}" '''
                        s3Download bucket: 'document-ekene', file: 'myproject.jar', path: 'ci-demo/javaapp/myapp.jar'
                    }
		 }
             }
        }
        stage('maven deploy') {
            steps {
		 script{
			if(TARGET_ENV == 'PROD'){
			    input('Do you want to proceed deployment to production?')
			}
			 
	                println("Deploying....\nMaven deploy...." )
	                echo"""mvn deploy:deploy-file -DgroupId=<group-id> \
                          -DartifactId=myapp.jar \
                          -Dversion=<version> \
                          -Dpackaging=<type-of-packaging> \
                          -Dfile=<path-to-file> \
                          -DrepositoryId=<id-to-map-on-server-section-of-settings.xml> \
                          -Durl=<url-of-the-repository-to-deploy>"""
		        }
		    }
        }
    }
    post{
        always{
                script{  
                    echo  '''this is always executed '''
		    cleanWs()
            }
        }
    }
}
