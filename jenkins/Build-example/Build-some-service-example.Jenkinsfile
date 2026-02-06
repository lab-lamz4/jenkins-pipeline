// Подключаем ранее сконфигурированные библиотеки
@Library('shared-libraries') _

pipeline {
    environment {
        TEST_ENV = "test"
    }

    agent {
        kubernetes {
            defaultContainer 'jnlp'
        }
    }

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '5', artifactNumToKeepStr: '5', daysToKeepStr: '5', numToKeepStr: '50')
        disableResume()
        skipDefaultCheckout true
        timeout(activity: true, time: 3, unit: 'HOURS')
        parallelsAlwaysFailFast()
    }
    parameters {
        string(
            name: 'parameter1',
            defaultValue: 'Parameter 1',
            description: 'Параметр для примера',
            trim: true
        )
        booleanParam(
            name: 'UPDATE_VARS',
            description: 'Выбрать если изменился пайплайн в git',
        )
    }
    stages {
        stage('✅ Update vars') {
            when {
              environment ignoreCase: true, name: 'UPDATE_VARS', value: 'true'
              beforeOptions true
              beforeAgent true
            }
            steps {
                script {
                    currentBuild.displayName = "${currentBuild.displayName}-update-vars"
                    currentBuild.result = 'SUCCESS'
                    return
                }
            }
        }
        stage (' 1️⃣ EXAMPLE STAGE 1') {
            when { expression {
                    !params.UPDATE_VARS
                }
            }
            steps {
                container('jnlp') {
                    script {
                        sh 'echo "EXAMPLE STAGE 1"'
                    }
                }
            }
        }
        stage(' 2️⃣ EXAMPLE STAGE 2') {
            when {
                expression {
                    !params.UPDATE_VARS
                }
            }
            steps {
                container('jnlp') {
                    script {
                        printMessage("✅ example params - ${params.parameter1}") {}
                    }
                }
            }
        }
    }
}
