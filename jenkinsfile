pipeline {
 agent any

    	        // Environment Variables
	        environment {
	        MAJOR = '1'
	        MINOR = '0'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/poctenda/DefaultTenant/orchestrator_/"
	        UIPATH_ORCH_LOGICAL_NAME = "carneiro.ac@gmail.com"
	        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
	        UIPATH_ORCH_FOLDER_NAME = "carneiro.ac@gmail.com's workspace (Converted)"
	    }
	

	    stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout scm
	

	            }
	        }
	

	         // Build Stages
	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
	                UiPathPack (
                      outputPath: "Output\\${env.BUILD_NUMBER}",
                      projectJsonPath: "project.json",
                      version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                      useOrchestrator: false,
					  traceLevel: 'None'
        )
	            }
	        }
	         // Test Stages
	        stage('Test') {
	            steps {
	                echo 'Testing..the workflow...'
	            }
	        }
	

	         // Deploy Stages
	        stage('Deploy to UAT') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to UAT "
                UiPathDeploy (
                packagePath: "Output\\${env.BUILD_NUMBER}",
                orchestratorAddress: "${UIPATH_ORCH_URL}",
                orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                environments: 'DEV',
                //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
                credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'), 
				traceLevel: 'None',
				entryPointPaths: 'Main.xaml'
	

	        )
	            }
	        }
	

	

	         // Deploy to Production Step
	        stage('Deploy to Production') {
	            steps {
					//mail bcc: '', body: 'Please check ${env.JOB_NAME} for approving deployment into Test stage.', cc:
					'', from: '', replyTo: '', subject: 'Jenkins Pipeline Approval Required', to: 'carneiro.ac@gmail.com'

					timeout(time: 14, unit: 'DAYS') {
						input message: 'Please approve the deployment of this package into Production', submitter: env.APPROVERS
					}

					build job: 'deploy-in-production', parameters: [string(name: 'PACKAGE_PATH', value: "${env.JENKINS_HOME}\\jobs\\${env.JOB_NAME}\\builds\\${env.BUILD_NUMBER}")]
	                echo 'Deploy to Production'
	                }
	            }
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo "Process ${env.GIT_URL} with version ${MAJOR}.${MINOR}.${env.BUILD_NUMBER} was successfully deployed into Production"
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
	        }
	    }
	

	}