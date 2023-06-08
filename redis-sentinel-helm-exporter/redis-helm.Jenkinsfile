def envName            = "${ENV_NAME}"
def appProjectName     = "${NAMESPACE_NAME}"
def storageClassName   = appProjectName+"-nfs"
def redisBaseImage     = "${REDIS_BASE_IMAGE}"
def redisExporterImage = "${REDIS_EXPORTER_IMAGE}"
def containerPlatform  = "${CONTAINER_PLATFORM}"
def needLoadBalancer   = "${NEED_LOAD_BALANCER}"
def loadbalancerIPs   = "${LOAD_BALANCER_IPS}"
def loadbalancerIP0 = "${loadbalancerIPs}".split(",")[0]
def loadbalancerIP1 = "${loadbalancerIPs}".split(",")[1]
def loadbalancerIP2 = "${loadbalancerIPs}".split(",")[2]
def status = 'FAILED'

node("maven") {
	try {
		dir('project'){		
			stage("Checkout yaml templates") {				
				sh "git config --global http.sslVerify false"

				if( envName.toString() in ["dev", "sit", "uat"] ){
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
                
                if( envName.toString() in ["prod1", "prod2"] ){
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

                if( envName.toString() in ["dr1"] ){
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
				print("needLoadBalancer:  ${needLoadBalancer}")
				if(needLoadBalancer == true) {
					print ("Need LoadBalancer")
					sh """
						/home/jenkins/.m2/helm/linux-amd64/helm install redis-sentinel-exporter \
							--set ocpProjectName="${appProjectName}" \
							--set redisBaseImage="${redisBaseImage}" \
							--set redisExporterImage="${redisExporterImage}" \
							--set containerPlatform="${containerPlatform}" \
							--set needLoadbalancer="true" \
							--set loadbalancerIP0="${loadbalancerIP0}" \
							--set loadbalancerIP1="${loadbalancerIP1}" \
							--set loadbalancerIP2="${loadbalancerIP2}" \
							--set storageClassName="${storageClassName}" \
							./redis-sentinel-helm-exporter/redis-sentinel-cluster \
							--kubeconfig="/home/jenkins/.m2/kubeconfig/${containerPlatform}/${envName}" -n "${appProjectName}" --wait --debug || \
						/home/jenkins/.m2/helm/linux-amd64/helm upgrade redis-sentinel-exporter \
							--set ocpProjectName="${appProjectName}" \
							--set redisBaseImage="${redisBaseImage}" \
							--set redisExporterImage="${redisExporterImage}" \
							--set containerPlatform="${containerPlatform}" \
							--set needLoadbalancer="true" \
							--set loadbalancerIP0="${loadbalancerIP0}" \
							--set loadbalancerIP1="${loadbalancerIP1}" \
							--set loadbalancerIP2="${loadbalancerIP2}" \
							--set storageClassName="${storageClassName}" \
							./redis-sentinel-helm-exporter/redis-sentinel-cluster \
							--kubeconfig="/home/jenkins/.m2/kubeconfig/${containerPlatform}/${envName}" -n "${appProjectName}" --wait --debug 
							
						"""		
				} else {
					sh """
						/home/jenkins/.m2/helm/linux-amd64/helm install redis-sentinel-exporter \
							--set ocpProjectName="${appProjectName}" \
							--set redisBaseImage="${redisBaseImage}" \
							--set redisExporterImage="${redisExporterImage}" \
							--set containerPlatform="${containerPlatform}" \
							--set storageClassName="${storageClassName}" \
                            ./redis-sentinel-helm-exporter/redis-sentinel-cluster \
                            --kubeconfig="/home/jenkins/.m2/kubeconfig/${containerPlatform}/${envName}" -n "${appProjectName}" --wait --debug || \
						/home/jenkins/.m2/helm/linux-amd64/helm upgrade redis-sentinel-exporter \
                            --set ocpProjectName="${appProjectName}" \
                            --set redisBaseImage="${redisBaseImage}" \
                            --set redisExporterImage="${redisExporterImage}" \
                            --set containerPlatform="${containerPlatform}" \
							--set storageClassName="${storageClassName}" \
                            ./redis-sentinel-helm-exporter/redis-sentinel-cluster \
                            --kubeconfig="/home/jenkins/.m2/kubeconfig/${containerPlatform}/${envName}" -n "${appProjectName}" --wait --debug 
						"""		
				}
        		
			}
		}
		
	    status = 'SUCCESS'
		
	} catch(Exception e) {
		echo e.toString()
		currentBuild.result = status
	}
}
