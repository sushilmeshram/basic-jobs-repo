pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "your-dockerhub-username/your-app"
    BRANCH_NAME = "${env.GIT_BRANCH}".split('/').last()
  }

  options {
    skipStagesAfterUnstable()
    timestamps()
  }

  tools {
    dockerTool 'docker'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Test') {
      parallel {
        stage('Unit Tests') {
          steps {
            sh 'echo "Running unit tests..."'
            // Example: sh './gradlew test'
          }
        }
        stage('Linting') {
          steps {
            sh 'echo "Running linter..."'
            // Example: sh 'eslint .'
          }
        }
      }
    }

    stage('Build & Archive') {
      steps {
        sh 'echo "Building artifact..."'
        // Example: sh './gradlew build'
        archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
      }
    }

    stage('Docker Build & Push') {
      when {
        branch 'main'
      }
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
            def image = docker.build("${DOCKER_IMAGE}:${BRANCH_NAME}-${env.BUILD_NUMBER}")
            image.push()
            image.push('latest')
          }
        }
      }
    }

    stage('Deploy') {
      steps {
        sh 'echo "Deploying to staging..."'
        // Example: ssh command to deploy to a remote server
      }
    }
  }

  post {
    always {
      cleanWs()
    }
    failure {
      echo 'Pipeline failed. Check logs above.'
    }
    success {
      echo 'Pipeline completed successfully!'
    }
  }
}
