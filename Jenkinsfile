pipeline {
  agent { node { label 'npm' } }
  environment {
  //last_tag = sh(returnStdout: true, script: "git describe --abbrev=0 --tags").trim()
    map_path = "/var/lib/workadventure-maps/" // TODO: this only works when workadventure is running on obby jobby and the user is allowed
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
    stage ('deploy maps to live') {
      when { branch 'master' }
      steps {
        sh "cp -v optimized/* ${map_path}/";
      }
    }
    stage ('deploy maps to snapshot') {
      when { not { branch 'master' } }
      steps {
        sh "mkdir -p ${map_path}/snapshot && cp -v optimized/* ${map_path}/snapshot/";
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

