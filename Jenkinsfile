


pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Detect Changes and Build') {
      steps {
        script {
          def rawOutput = sh(
            script: "git diff --name-only HEAD~1 HEAD",
            returnStdout: true
          ).trim()

          def folders = rawOutput.split("\n").collect { it.split("/")[0] }.unique().findAll { folder ->
            fileExists("${folder}/Dockerfile")
          } as List  // <-- Make sure it's a normal list

          def builds = [:]
          for (int i = 0; i < folders.size(); i++) {
            def folder = folders[i]  // Define it inside the loop
            builds["Build ${folder}"] = {
              dir(folder) {
                sh "docker build -t myrepo/${folder}:latest ."
              }
            }
          }

          if (builds) {
            parallel builds
          } else {
            echo "No changes found. Skipping builds."
          }
        }
      }
    }
  }
}

