pipeline {
    agent any

    parameters {
        choice(
            choices: ['CheckoutSCM', 'test_server'],
            description: '\n test_server - its deploy from 95.217.232.79',
            name: 'namespace'
        )
        booleanParam(
            defaultValue: false,
            description: 'Enable custom command execution',
            name: 'enableCustomCommand'
        )
        string(
            defaultValue: '',
            description: 'Custom command to execute (if enabled)',
            name: 'customCommand'
        )
    }

    stages {
        stage("Deploy test_server with changes") {
            when {
                expression { params.namespace == 'test_server' }
            }
            steps {
                script {
                    echo "----------Start build---------"
                    deployTestServer()
                    echo "----------check git branch---------"
                    checkGitBranch()
                    echo "----------End build---------"
                }
            }
        }

        stage('Checkout SCM') {
            when {
                expression { params.namespace == 'CheckoutSCM' }
            }
            steps {
                script {
                    echo 'CheckoutSCM'
                }
            }
        }

        stage('Execute Custom Command') {
            when {
                expression { params.enableCustomCommand && params.namespace == 'test_server' }
            }
            steps {
                script {
                    echo "Executing custom command: ${params.customCommand}"
                    sh "${params.customCommand}"
                }
            }
        }

        stage('Restart Services') {
            when {
                expression { params.restartGunicorn }
            }
            steps {
                script {
                    restartServices()
                }
            }
        }
    }
}

def deployTestServer() {
    sshCommand("cd /mnt/test_projects/sites/test_byme/ && ls")
}

def checkGitBranch() {
    sshCommand("cd /mnt/test_projects/sites/test_byme/ && git branch")
}

def sshCommand(command) {
    sh "ssh -i /var/jenkins_home/.ssh/id_rsa -p 48999 root@95.217.232.79 '${command}'"
}

def restartServices() {
    echo "Restarting Gunicorn..."
    sshCommand("systemctl restart byme")
}
