// vim: et:ts=4:sw=4:syntax=groovy

pipeline {
  agent { node { label 'npm' } }
  environment {
  //last_tag = sh(returnStdout: true, script: "git describe --abbrev=0 --tags").trim()
    map_path = "/var/lib/workadventure-maps/" // TODO: this only works when workadventure is running on obby jobby and the user is allowed
    play_url = "${GIT_BRANCH == "master" ?  "https://snapshot-work.search4ce.app.de.corp.thales" : "https://snapshot-work.search4ce.app.de.corp.thales"}"
    changelog = getChangeString()
  }
  options {
    disableConcurrentBuilds()
    skipStagesAfterUnstable()
  }
  stages {
    stage ('optimize maps') {
      steps {
        // TODO: all available maps
        sh '''
            for i in *.json;do
                echo "optimizing $i"
                tile-optimizer -i $i -o optimized
            done
            cp -vr static optimized/
        ''';

      }
    }

    stage ('render screenshots of maps') {
      steps {
        sh '''
          mkdir -p screenshots/
          for i in optimized/*.json;do
              echo "rendering screenshot of $i"
              xvfb-run tmxrasterizer --ignore-visibility "$i" "screenshots/$(basename $i | sed 's/.json$//').png"
          done
        ''';

      }
    }
    stage ('deploy maps to live') {
      when { branch 'master' }
      steps {
        sh "cp -vr optimized/* ${map_path}/";
      }
    }
    stage ('deploy maps to snapshot') {
      when { not { branch 'master' } }
      steps {
        sh "mkdir -p ${map_path}/snapshot && cp -rv optimized/* ${map_path}/snapshot/";
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'optimized/*'
      archiveArtifacts artifacts: 'screenshots/*.png'
      // TODO: show all screenshots by iterating over screenshots/*.png
      echo "For GIT Branch: ${GIT_BRANCH}"
      sh "(echo '${currentBuild.fullDisplayName}: Finished Build with status ${currentBuild.result} (took ${currentBuild.durationString})';echo 'Changes:\n${changelog}Main: ${currentBuild.absoluteUrl}/artifact/screenshots/entry.png\nEE: ${currentBuild.absoluteUrl}/artifact/screenshots/ee.png\nJoin via: ${env.play_url}\nBuild URL: ${currentBuild.absoluteUrl}') | announce_config=/etc/workadventure-announce.cfg citadel-send-announce"
    }
  }

}

@NonCPS
def getChangeString() {
    MAX_MSG_LEN = 100
    def changeString = ""

    echo "Gathering SCM changes"
    def changeLogSets = currentBuild.changeSets
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            truncated_msg = entry.msg.take(MAX_MSG_LEN)
            changeString += " - ${truncated_msg} [${entry.author}]\n"
        }
    }

    if (!changeString) {
        changeString = " - No new changes\n"
    }
    return changeString
}
