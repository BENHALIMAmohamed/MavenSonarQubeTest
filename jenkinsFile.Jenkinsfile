//https://medium.com/devoops-and-universe/jenkins-integration-with-sonarqube-2e9fb3d18919
pipeline {
    agent {label 'master'}

    stages {
        stage('checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/BENHALIMAmohamed/MavenSonarQubeTest.git']]])            }
        }
        stage('Build project') {
            steps {
            bat '''mvn clean /
                    mvn package /
                    mvn install'''
            }
        }
        stage('SonarQube analysis') {
            environment {
            SCANNER_HOME = tool 'Sonar-scanner'
            }
            steps {
            withSonarQubeEnv(credentialsId: 'sonar-credentialsId', installationName: 'Sonar') {
                sh '''$SCANNER_HOME/bin/sonar-scanner \
                -Dsonar.projectKey=projectKey \
                -Dsonar.projectName=projectName \
                -Dsonar.sources=src/ \
                -Dsonar.java.binaries=target/classes/ \
                -Dsonar.exclusions=src/test/java/****/*.java \
                -Dsonar.java.libraries=/var/lib/jenkins/.m2/**/*.jar \
                -Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}'''
            }
            }
        }
        stage('SQuality Gate') {
            steps {
            timeout(time: 1, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
            }
        }
}
}
}