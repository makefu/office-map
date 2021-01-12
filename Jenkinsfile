pipeline {
  agent { node { label 'npm' } }
  environment {
  //last_tag = sh(returnStdout: true, script: "git describe --abbrev=0 --tags").trim()
    map_path = "/var/lib/workadventure-maps/" # TODO: this only works when workadventure is running on obby jobby and the user is allowed
  }
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
    stage ('deploy maps') {
      steps {
        sh "cp -v optimized/* ${map_path}/";
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'optimized/*'
      sh "(echo '${currentBuild.fullDisplayName}: Finished Build with status ${currentBuild.result} (took ${currentBuild.durationString})\nTest Report:';echo 'Changes:\n${changelog}See also ${currentBuild.absoluteUrl}') | citadel-send-announce"
    }
  }

}

