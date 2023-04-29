def ocpEnvName         = "${OCP_ENV_NAME}"
def appProjectName     = "${OCP_PROJECT_NAME}"
def redisBaseImage     = "${REDIS_BASE_IMAGE}"
def redisExporterImage = "${REDIS_EXPORTER_IMAGE}"

node("maven") {
	try {
		dir('project'){		
			stage("Checkout yaml templates") {				
				sh "git config --global http.sslVerify false"

				if( ocpEnvName.toString() in ["dev", "sit", "uat"] ){
                    def scmUrlOCP        = "https://esbscmg.testesunbank.com.tw/up0094/redis-cd.git"
                    def gitCredentialsId = "0a37f33a-72f3-4ce6-b677-2a9a333e8e03"				
    				checkout([
    						$class:       'GitSCM', 
    						branches:     [[name: "*/develop"]], 
    						doGenerateSubmoduleConfigurations: false, 
    						extensions:   [], 
    						submoduleCfg: [], 
    						userRemoteConfigs: [[credentialsId: gitCredentialsId, url: scmUrlOCP]]
    				])  
                }
                
                if( ocpEnvName.toString() in ["prod1", "prod2"] ){
                    def scmUrlOCP        = "https://gitlabp.esunbank.com.tw/up0094/redis-cd.git"
                    def gitCredentialsId = "6e440caa-eab9-4ca9-9a20-3ea6760c3d78"				
    				checkout([
    						$class:       'GitSCM', 
    						branches:     [[name: "*/master"]], 
    						doGenerateSubmoduleConfigurations: false, 
    						extensions:   [], 
    						submoduleCfg: [], 
    						userRemoteConfigs: [[credentialsId: gitCredentialsId, url: scmUrlOCP]]
    				])                      
                }

                if( ocpEnvName.toString() in ["dr1"] ){
                    def scmUrlOCP        = "https://gitlabdr.esunbank.com.tw/up0094/redis-cd.git"
                    def gitCredentialsId = "6e440caa-eab9-4ca9-9a20-3ea6760c3d78"				
    				checkout([
    						$class:        'GitSCM', 
    						branches:      [[name: "*/master"]], 
    						doGenerateSubmoduleConfigurations: false, 
    						extensions:    [], 
    						submoduleCfg:  [], 
    						userRemoteConfigs: [[credentialsId: gitCredentialsId, url: scmUrlOCP]]
    				])                      
                }
                
                sh "git status"
			}	
			
			stage("Redis Deploy"){
        		sh """
        		/home/jenkins/.m2/helm/linux-amd64/helm install redis-sentinel-exporter --set ocpProjectName="${appProjectName}",redisBaseImage="${redisBaseImage}",redisExporterImage="${redisExporterImage}" ./OCP4-redis-sentinel-helm-exporter/helm-yaml -n "${appProjectName}" --wait --debug || \
        		/home/jenkins/.m2/helm/linux-amd64/helm upgrade redis-sentinel-exporter --set ocpProjectName="${appProjectName}",redisBaseImage="${redisBaseImage}",redisExporterImage="${redisExporterImage}" ./OCP4-redis-sentinel-helm-exporter/helm-yaml -n "${appProjectName}" --wait --debug
        		"""		
			}
		}
		
		status = 'SUCCESS'
		
	} catch(Exception e) {
		echo e.toString()
		currentBuild.result = status
	}
}
