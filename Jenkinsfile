pipeline {
   agent any
	stages {
		stage('Git Checkout'){
			steps {
				git 'https://github.com/madhumita-kundo/Event-Management.git'
				}
		}
		stage('compile'){
			steps {
				sh 'python -m py_compile sources/app.py' 
        stash(name: 'compiled-results', includes: 'sources/*.py*') '
				}
		}
    		stage('test'){
			steps {
				sh 'python3 /test.py'
				}
		}
		stage('docker build'){
			steps {
				sh 'docker build -t Event-management:${BUILD_NUMBER} .'
				}
		}
    stage('docker push'){
    steps{
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
     stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
	
		stage('Trigger process in UcDeploy'){
			 step([$class: 'UCDeployPublisher',
        siteName: '35.242.161.163',
        component: [
            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
            componentName: 'Event Management Service Component'
        ],
        deploy: [
            $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
            deployApp: 'Full Application',
            deployEnv: 'DEV',
            deployProc: 'Deploy Application',
            deployVersions: '${component_name}_${BUILD_NUMBER}',
            deployOnlyChanged: false
        ]
    ])
}
	}
}
