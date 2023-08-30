pipeline {
    agent any
    parameters {
        choice(
            choices: ['staging','pre_productions'],
            description: '\n dev - its deploy from 161.35.100.107 ',
            name: 'namespace'
        )
        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        string(name: 'servers', defaultValue: 'server2 server2', description: 'Separate server name by space')
         booleanParam(name: 'copyToServers', defaultValue: false, description: 'whether to copy the builds to the servers. Skip this step when you are not on network')

        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }
    stages {
        stage('Example') {
                    when {
              allOf {
                  expression { params.namespace == 'staging' }
              }
          }
            steps {
                echo "Hello ${params.PERSON}"

                echo "Biography: ${params.BIOGRAPHY}"

                echo "Toggle: ${params.TOGGLE}"

                echo "Choice: ${params.CHOICE}"

                echo "Password: ${params.PASSWORD}"
            }
        }
        stage("Deploy to preprod with changes") {
    when {
        expression { params.namespace == 'pre_productions' }
    }
    steps {
        script {
            echo "----------Start build---------"
            def userInput = input(
                id: 'deployConfirmation',
                message: 'Do you want to proceed with the build?',
                parameters: [
                    [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Proceed?', name: 'confirm']
                ]
            )
            if (userInput.confirm) {
                sh '''
                    ssh -i /var/lib/jenkins/.ssh/id_rsa dudka@161.35.100.107 "ls"
                '''
                echo "----------check git branch---------"
                sh '''
                    ssh -i /var/lib/jenkins/.ssh/id_rsa dudka@161.35.100.107 "ls"
                '''
                echo "----------End build---------"
            } else {
                echo "Build canceled."
            }
        }
    }
}

        
                stage('test') {
                    when {
              allOf {
                  expression { params.namespace == 'staging' }
              }
          }
            steps {
                echo 'Hello World'
            }
        }
    }
    
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
