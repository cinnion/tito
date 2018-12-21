#!/usr/bin/env groovy

pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                sh '''
                    env
                    set
                    ls -la
                    ls -laR
                    git describe HEAD
                '''
            }
        }
    }
}
