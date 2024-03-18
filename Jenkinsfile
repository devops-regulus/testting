pipeline {
    agent any

    parameters {
        choice(
            choices: ['CheckoutSCM', 'test_server'],
            description: '\n test_server - розгорнути з 95.217.232.79',
            name: 'namespace',
            defaultValue: 'test_server'
        )
        booleanParam(
            defaultValue: false,
            description: 'Увімкнути виконання користувацької команди',
            name: 'enableCustomCommand'
        )
        string(
            defaultValue: '',
            description: 'Користувацька команда для виконання (якщо увімкнено)',
            name: 'customCommand'
        )
    }

    stages {
        stage("Розгорнути test_server зі змінами") {
            when {
                expression { params.namespace == 'test_server' }
            }
            steps {
                script {
                    echo "----------Почати збірку---------"
                    deployTestServer()
                    echo "----------Перевірити гілку git---------"
                    checkGitBranch()
                    echo "----------Завершити збірку---------"
                }
            }
        }

        stage('Перевірка SCM') {
            when {
                expression { params.namespace == 'CheckoutSCM' }
            }
            steps {
                script {
                    echo 'CheckoutSCM'
                }
            }
        }

        stage('Виконати користувацьку команду') {
            when {
                expression { params.enableCustomCommand && params.namespace == 'test_server' }
            }
            steps {
                script {
                    echo "Виконання користувацької команди: ${params.customCommand}"
                    sh "${params.customCommand}"
                }
            }
        }

        stage('Перезапуск сервісів') {
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

    post {
        always {
            echo 'Це завжди буде виконуватися'
        }
        success {
            echo 'Це буде виконуватися лише при успішному виконанні'
        }
        failure {
            echo 'Це буде виконуватися лише при помилці'
        }
        unstable {
            echo 'Це буде виконуватися лише якщо виконання було позначено як нестабільне'
        }
        changed {
            echo 'Це буде виконуватися лише якщо стан конвеєра змінився'
            echo 'Наприклад, якщо конвеєр раніше завершувався з помилкою, але зараз успішний'
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
    println "Виконання SSH команди: ${command}"
    sh "ssh -i /var/jenkins_home/.ssh/id_rsa -p 48999 root@95.217.232.79 '${command}'"
}

def restartServices() {
    println "Перезапуск Gunicorn..."
    sshCommand("systemctl restart byme")
}
