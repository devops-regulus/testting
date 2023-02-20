pipeline {
 agent{
	label 'jenServ'
	}
    parameters {

        choice(
                choices: ['CheckoutSCM','rebuild'],
                description: '\n dev - its deploy from 143.198.164.231 ',
                name: 'namespace'
        )
    }

  stages {	  
			  
	  stage('Rebuild front ') {
			when {
              allOf {
                  expression { params.namespace == 'rebuild' }
              }
          }
            steps {
				echo "----------Changes not found---------"
                 sh'/home/jenkins/prod/prod_dudka.sh'
				 echo "----------End rebuild---------"
            }
        }

	  stage('Checkout SCM') {
	when {
              allOf {
                  expression { params.namespace == 'CheckoutSCM' }
              }
          }
            steps {
                echo 'CheckoutSCM'
            }
        }
  }
}
