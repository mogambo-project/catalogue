// Catalogue is a Go (GOPATH-era) service. It is built from its own Dockerfile so the
// pipeline doesn't depend on a Jenkins-global Go tool. Mirrors the old catalogue-dockerbuild
// job's deliverable (the andriohanania/catalogue image) but as version-controlled pipeline code.
// Tweak stages/targets as the service evolves.
pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }
  environment {
    IMAGE = 'andriohanania/catalogue'
  }
  stages {
    stage('build image') {
      steps {
        sh 'docker build -f docker/catalogue/Dockerfile -t $IMAGE:$BUILD_NUMBER -t $IMAGE:latest .'
      }
    }
  }
  post {
    success { echo "Built ${IMAGE}:${BUILD_NUMBER}" }
    failure { echo 'Catalogue build failed - check the stage log.' }
  }
}
