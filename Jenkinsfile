pipeline {
    agent any

    parameters {
        choice(
            choices: ['CheckoutSCM', 'test_server'],
            description: '\n test_server - развернуть с 95.217.232.79',
            name: 'namespace'
        )
        booleanParam(
            defaultValue: false,
            description: 'Включить выполнение пользовательской команды',
            name: 'enableCustomCommand'
        )
        string(
            defaultValue: '',
            description: 'Пользовательская команда для выполнения (если включено)',
            name: 'customCommand'
        )
    }

    stages {
        stage("Развернуть test_server с изменениями") {
            when {
                expression { params.namespace == 'test_server' }
            }
            steps {
                script {
                    echo "----------Начать сборку---------"
                    deployTestServer()
                    echo "----------Проверить ветку git---------"
                    checkGitBranch()
                    echo "----------Завершить сборку---------"
                }
            }
        }

        stage('Проверка SCM') {
            when {
                expression { params.namespace == 'CheckoutSCM' }
            }
            steps {
                script {
                    echo 'CheckoutSCM'
                }
            }
        }

        stage('Выполнить пользовательскую команду') {
            when {
                expression { params.enableCustomCommand && params.namespace == 'test_server' }
            }
            steps {
                script {
                    echo "Выполнение пользовательской команды: ${params.customCommand}"
                    sh "${params.customCommand}"
                }
            }
        }

        stage('Перезапуск сервисов') {
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
            echo 'Это всегда будет выполняться'
        }
        success {
            echo 'Это будет выполняться только при успешном выполнении'
        }
        failure {
            echo 'Это будет выполняться только при ошибке'
        }
        unstable {
            echo 'Это будет выполняться только если выполнение было помечено как нестабильное'
        }
        changed {
            echo 'Это будет выполняться только если состояние конвейера изменилось'
            echo 'Например, если конвейер ранее завершался с ошибкой, но сейчас успешно'
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
    println "Выполнение SSH команды: ${command}"
    sh "ssh -i /var/jenkins_home/.ssh/id_rsa -p 48999 root@95.217.232.79 '${command}'"
}

def restartServices() {
    println "Перезапуск Gunicorn..."
    sshCommand("systemctl restart byme")
}
