pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    environment {
        APP_NAME    = 'maven-java-monitoring-example'
        APP_VERSION = '1.0.0'
    }

    stages {

        stage('Checkout') {
            steps {
                echo '========== Pulling latest code from Git =========='
                checkout scm
                echo "Branch: ${env.GIT_BRANCH}"
                echo "Commit: ${env.GIT_COMMIT}"
            }
        }

        stage('Build') {
            steps {
                echo '========== Compiling the Java project =========='
                bat 'mvn clean compile -DskipTests'
            }
            post {
                failure {
                    echo 'Build stage FAILED! Stopping pipeline.'
                }
            }
        }

        stage('Test') {
            steps {
                echo '========== Running Unit Tests =========='
                bat 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
                failure {
                    echo 'Tests FAILED! Check test reports.'
                }
            }
        }

        stage('Package') {
            steps {
                echo '========== Creating deployable JAR =========='
                bat 'mvn package -DskipTests'
            }
        }

        stage('Deploy') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo '========== SIMULATED DEPLOYMENT =========='
                echo "Deploying ${APP_NAME} version ${APP_VERSION}"
                bat 'if exist "C:\deploy" rmdir /s /q "C:\deploy"'
                bat 'mkdir C:\deploy'
                bat 'copy target\*.jar C:\deploy\'
                echo 'Deployment simulation complete! Files copied to C:\deploy'
            }
        }

    }

    post {
        always {
            echo '========== Archiving Build Artifacts =========='
            archiveArtifacts artifacts: "target/*.jar", allowEmptyArchive: true
        }

        success {
            echo 'PIPELINE SUCCEEDED!'
            mail to: 'avnssvasisht@gmail.com',
                 subject: "SUCCESS: ${APP_NAME} build #${env.BUILD_NUMBER}",
                 body: "Build succeeded! View: ${env.BUILD_URL}"
        }
        failure {
            echo 'PIPELINE FAILED!'
            mail to: 'avnssvasisht@gmail.com',
                 subject: "FAILURE: ${APP_NAME} build #${env.BUILD_NUMBER}",
                 body: "Build failed! View: ${env.BUILD_URL}"
        }
    }
}
