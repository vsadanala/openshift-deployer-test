node {
  
	// Checkout from SCM
  	stage ("Checkout") {
  		checkout scm
  	}
  // Read From Properties
	  projects = readFile("${env.BRANCH_NAME}-projects.properties") 
	  environments = readFile("${env.BRANCH_NAME}-environments.properties") 
	  commands = ['ReDeploy', 'Remove']
	  properties([
	       parameters([
	       choice( choices: commands,      description: 'Command',   name: 'Command'), 
  	       choice( choices: projects,      description: 'Service',   name: 'Service'), 
  	       choice( choices: environments,  description: 'Environment', name: 'Environment')
	       ]), 
	       pipelineTriggers([])
	  ])
  	stage ("${env.Command} ${env.Service} in ${env.Environment} " ) {
  	  String service = "${env.Service}";
  	  String namespace = "${env.Environment}";
  	  String command = "${env.Command}";
  	  int waitTime = 10*60*1000 //10 min
          def deploydef = readFile("${WORKSPACE}/${env.Service}/deploy.yaml")
	  def serverDef = readFile("${WORKSPACE}/${env.Service}/service.yaml")

      if ( service != "" && namespace != "" ) { 
        if ( command.equals('ReDeploy') ) { 
            echo "Redeploy service " + service + " to " + namespace; 
          
          
          script {
          openshift.withCluster() {
            openshift.tag("image-registry.openshift-image-registry.svc:5000/development/hello-react:latest", "${env.Environment}/development:production")
            
          }
        }
        }
          
        if ( command.equals('Remove') ) { 
            echo "Remove service " + service + " to " + namespace; 
            openshiftDeleteResourceByJsonYaml(
              yaml: "${deploydef}",
              waitTime: waitTime)
			openshiftDeleteResourceByJsonYaml(
              yaml: "${serverDef}",
              waitTime: waitTime)
			
			
	    }
      }
  	}
}
