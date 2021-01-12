pipeline {
  agent { node { label 'npm' } }
  //environment {
  //  //last_tag = sh(returnStdout: true, script: "git describe --abbrev=0 --tags").trim()
  //}
  options {
    disableConcurrentBuilds()
    skipStagesAfterUnstable()
  }
  stages {
    stage ('optimize maps') {
      steps {
        // TODO: all available maps
        sh "tile-optimizer -i entry.json -o optimized";
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'optimized/*'
    }
  }

}

