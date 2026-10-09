// Catalogue is a Go (GOPATH-era) service, built from the Dockerfiles in this repo.
// This pipeline builds & pushes BOTH the catalogue app image and the catalogue-db image
// (its Dockerfile also lives here), replacing the old standalone catalogue-dockerbuild
// and catalogue-db-dockerbuild jobs with version-controlled pipeline code.
pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }

  environment {
    REGISTRY       = 'https://index.docker.io/v1/'
    DOCKERHUB_CRED = 'bb835d88-f2f7-402f-a964-a492f5cd7cbf'
    IMAGE_APP      = 'andriohanania/catalogue'
    IMAGE_DB       = 'andriohanania/catalogue-db'
  }

  stages {
    stage('docker build & push') {
      // Build only from version branches: 1.0, 2.2, ...  (no 'master' anymore)
      when { branch pattern: '\\d+\\.\\d+', comparator: 'REGEXP' }
      steps {
        script {
          // version from the VERSION file (keep its major.minor == the branch name, e.g. branch 1.0 -> 1.0.x)
          def ver = readFile('VERSION').trim()
          def tag = "${ver}-${env.BUILD_NUMBER}"   // e.g. 1.0.0-7  (immutable, branch-scoped)
          docker.withRegistry(env.REGISTRY, env.DOCKERHUB_CRED) {
            // app image
            def app = docker.build("${env.IMAGE_APP}:${tag}", "-f docker/catalogue/Dockerfile .")
            app.push()           // the only tag we push now — no moving ':latest'
            // db image (folds in the old catalogue-db-dockerbuild job)
            def db = docker.build("${env.IMAGE_DB}:${tag}", "-f docker/catalogue-db/Dockerfile .")
            db.push()
          }
          echo "Pushed ${env.IMAGE_APP}:${tag} and ${env.IMAGE_DB}:${tag}"
        }
      }
    }
  }

  post {
    failure { echo 'Catalogue build failed - check the stage log.' }
  }
}
