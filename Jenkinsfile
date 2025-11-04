pipeline {
    agent any

    tools {
        maven 'mvn3'
        jdk 'jdk17'
    }

    environment {
        // Optional if Nexus is used
        NEXUS_CREDENTIALS = credentials('nexus')
        NEXUS_URL = 'http://98.82.189.238:8081//repository/maven-releases/'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo '🔁 Checking out code from GitHub...'
                git branch: 'main', url: 'https://github.com/kothapalli1094/shiva-app.git'
            }
        }

        stage('Build') {
            steps {
                echo '🏗️ Building project using Maven...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running unit tests...'
                sh 'mvn test'
            }
        }

        stage('Archive Artifact') {
            steps {
                echo '📦 Archiving build artifacts...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Deploy to Nexus (Optional)') {
            when {
                expression { return env.NEXUS_URL != null }
            }
            steps {
                echo '🚀 Uploading artifact to Nexus...'
                sh """
                mvn deploy:deploy-file \
                    -DgroupId=com.example \
                    -DartifactId=myapp \
                    -Dversion=1.0.${BUILD_NUMBER} \
                    -Dpackaging=jar \
                    -Dfile=target/*.jar \
                    -DrepositoryId=nexus \
                    -Durl=${NEXUS_URL}
                """
            }
        }
    }

    post {
        success {
            echo '✅ CI pipeline completed successfully!'
        }
        failure {
            echo '❌ CI pipeline failed — check logs for details.'
        }
    }
}
