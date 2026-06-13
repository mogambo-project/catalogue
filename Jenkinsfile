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
      when { branch 'master' }   // build/push images only from the main line, not from PRs
      steps {
        script {
          def gitsha = (env.GIT_COMMIT ?: 'dev').take(7)
          def tag    = "${env.BUILD_NUMBER}-${gitsha}"
          docker.withRegistry(env.REGISTRY, env.DOCKERHUB_CRED) {
            // app image
            def app = docker.build("${env.IMAGE_APP}:${tag}", "-f docker/catalogue/Dockerfile .")
            app.push()           // immutable tag
            app.push('latest')   // moving tag
            // db image (folds in the old catalogue-db-dockerbuild job)
            def db = docker.build("${env.IMAGE_DB}:${tag}", "-f docker/catalogue-db/Dockerfile .")
            db.push()
            db.push('latest')
          }
        }
      }
    }
  }

  post {
    failure { echo 'Catalogue build failed - check the stage log.' }
  }
}
