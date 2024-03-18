pipeline {
    agent any

    parameters {
        choice(
            choices: ['CheckoutSCM', 'test_server'],
            description: '\n test_server - розгорнути з 95.217.232.79',
            name: 'namespace'
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
                    echo "\033[34m----------Почати збірку---------"
                    deployTestServer()
                    echo "----------Перевірити гілку git---------"
                    checkGitBranch()
                    echo "----------Завершити збірку---------\033[0m"
                }
            }
        }

        stage('Перевірка SCM') {
            when {
                expression { params.namespace == 'CheckoutSCM' }
            }
            steps {
                script {
                    echo '\033[34mCheckoutSCM\033[0m'
                }
            }
        }

        stage('Виконати користувацьку команду') {
            when {
                expression { params.enableCustomCommand && params.namespace == 'test_server' }
            }
            steps {
                script {
                    echo "\033[34mВиконання користувацької команди: ${params.customCommand}\033[0m"
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
            echo '\033[34mЦе завжди буде виконуватися\033[0m'
        }
        success {
            echo '\033[34mЦе буде виконуватися лише при успішному виконанні\033[0m'
        }
        failure {
            echo '\033[34mЦе буде виконуватися лише при помилці\033[0m'
        }
        unstable {
            echo '\033[34mЦе буде виконуватися лише якщо виконання було позначено як нестабільне\033[0m'
        }
        changed {
            echo '\033[34mЦе буде виконуватися лише якщо стан конвеєра змінився\033[0m'
            echo '\033[34mНаприклад, якщо конвеєр раніше завершувався з помилкою, а тепер успішно\033[0m'
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
    println "\033[34mВиконання SSH команди: ${command}\033[0m"
    sh "ssh -i /var/jenkins_home/.ssh/id_rsa -p 48999 root@95.217.232.79 '${command}'"
}

def restartServices() {
    println "\033[34mПерезапуск Gunicorn...\033[0m"
    sshCommand("systemctl restart byme")
}
