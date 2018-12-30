#!/usr/bin/env groovy

pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    environment {
        REPO_BASE_DIR = '/repos/local/centos/7'
        GIT_DESCRIPTION = """${sh( returnStdout: true, script: 'git describe HEAD 2>/dev/null || :' )}"""
        TITO_TEST = """${sh( returnStatus: true, script: '(git describe HEAD 2>/dev/null || :) | egrep -q -v -- "-g[0-9a-f]{7,}$"' )}"""
        DEBUG = '1'
    }

    stages {
        stage('Checkout') {
            steps {
                sh '''
                    pwd
                    env
                    set
                    ls -la
                    ls -laR
                    git describe HEAD
                '''
            }
        }
        stage('Build test version') {
            when { environment name: 'TITO_TEST', value: '1' }
            steps {
                sh 'tito build --rpm --dist=.ka8zrt.el7 --test --output=tito.out'
            }
        }

        stage('Build release version') {
            when { environment name: 'TITO_TEST', value: '0' }
            steps {
                sh 'tito build --rpm --dist=.ka8zrt.el7 --output=tito.out'
            }
        }

        stage('Tests') {
            steps {
                sh 'hacking/runtests.sh'
            }
        }
    }

    post {
        always {
            sh '''
                env
                set
                ls -la
                ls -laR
            '''
        }
        success {
            sh 'rm -rf ${REPO_BASE_DIR}/noarch/Packages/tito*'
            sh 'cp -p tito.out/noarch/tito*.noarch.rpm ${REPO_BASE_DIR}/noarch/Packages/'
            sh 'cp -p tito.out/*.src.rpm ${REPO_BASE_DIR}/Sources/SPackages/'
            sh '/usr/local/bin/createrepo2 ${REPO_BASE_DIR}'
            sh 'rpm -q -i -p tito.out/noarch/tito*.noarch.rpm'
            sh 'rpm -q -i -p tito.out/tito*.src.rpm'
        }
    }
}
