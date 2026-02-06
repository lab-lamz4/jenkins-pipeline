// Подключаем ранее сконфигурированные библиотеки
@Library('shared-libraries') _

pipeline {
    environment {
        DEPLOY_TO_ENV = "staging"
    }

    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    job: build-service
    label: jenkins-agent
spec:
  containers:
  - name: deploy
    image: curlimages/curl
    resources:
      limits:
        cpu: 400m
        memory: 600Mi
      requests:
        cpu: 100m
        memory: 100Mi
    command:
    - cat
    tty: true
"""
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
        stage (' 🥁 EXAMPLE DEPLOY STAGE') {
            when { expression {
                    !params.UPDATE_VARS
                }
            }
            steps {
                container('jnlp') {
                    script {
                        printMessage("✅ Deploy example - ${DEPLOY_TO_ENV}") {}
                    }
                }
            }
        }
    }
}
