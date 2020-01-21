import groovy.json.JsonSlurperClassic

@NonCPS
def jsonParse(def json){
	new groovy.json.JsonSlurperClassic().parseText(json)
}

env.gitRepo = "http://gogs/AZC/azure-dsl.git"

node(){
	
	stage('Prepare'){
		step([$class: 'WsCleanup'])

		checkout([

			$class: 'GitSCM',
			branches: [[name: env.GIT_BRANCH]],
			extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: '.']],
			userRemoteConfigs: [[credentialsID: 'vagrant', url: gitRepo]]

		])
	}


	def dslJobs = jsonParse(readFile('dslJobs.json'))

	for (item in dslJobs.items){
		if(item.folders.contains(env.ENVIRONMENT.toUpperCase())){
			stage(item.name){
				step([
					$class: 'ExecuteDslScripts',
					targets: item.target,
					lookupStrategy: 'SEED_JOB'
				])
			}
		}
	}

	stage('Cleanup'){
		step([$class: 'WsCleanup'])
	}
}
