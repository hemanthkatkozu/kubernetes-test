pipeline {
    agent any
    
    parameters {
        string(name: 'KUBE_API_EP', defaultValue: 'https://k8api-ss-761022775619-12fc0173b429a979.elb.us-east-1.amazonaws.com', description: 'provide the image tag to be deployed')
	choice(name: 'service_name', choices: ['alert-service-2-0','audit-ingestion','bh-data-filtering-config-service','bh-security-service','bh-user-mgmt-service','bh-tenant-mgmt-service','bh-demo-app-node-adapter','demo-app-client-adapter','bh-demo-app-java-adapter','keycloak','nginx','bh-tenant-onboarding','cde-app-shell'] , description: 'Add/Select the Deployment Name of the Micro Service which you want to restart')
        string(name: 'cert_name', defaultValue: 'deploy-qa.crt', description: 'Add/Select the K8s CERT filename of the cluster you want to connect') 
    }
    
    // define the credentials to be used according to the project requirements in environment.
    environment {
        CERT = "$WORKSPACE/certs/${cert_name}"
    }
    stages{
        stage('Check prerequisites'){
            steps{
                script{
                    cleanWs()
	                if ("${params.KUBE_API_EP}" == ''){
                    currentBuild.result = 'ABORTED'
                    error('K8s API Endpoint is not provided !!!')
                    }
                }
            }
        }
        stage ('git checkout') {
            steps {
                git 'https://github.com/hemanthkatkozu/kubernetes-test.git'
            }
        }
        stage("Restart POD")
		{
			steps{
                withCredentials([string(credentialsId: 'K8S-TOKEN', variable: 'KUBE_API_TOKEN')]) {
                // git branch: "main" , credentialsId: 'NEW_BH_GIT', url: "https://github.com/bh-ent-tech/sparq-security-argocd"
                sh '''
                echo "Setup Cluster configuration..."
                #CERT="$WORKSPACE/certs/deploy-dev.crt"
                sh $WORKSPACE/certs/set-k8s-context.sh $KUBE_API_EP $KUBE_API_TOKEN $CERT
                 
                kubectl get nodes --insecure-skip-tls-verify
                '''
                }
            }
        }
    }
        post {
            always {
               cleanWs()
            }
        }
	}  
