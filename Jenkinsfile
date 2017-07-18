
def stepsForParallel = [:]

def dbNames = ['nconf', 'phpmyadmin', 'zen']
for (int i=0; i<dbNames.size(); ++i) {
   def name = dbNames[i]
   stepsForParallel[name] = transformIntoStep(name)
}

// return a closure because we do not want 'node(..){...}' execcuted when this function is called
def transformIntoStep(dbName) {
  return {
    "${dbName}": {
      def opfile = "${dbName}.sql"
      sh "sudo docker exec -i mysql mysqldump --user bobb --password=${PWRD}  ${dbName} > ${dbName}.sql"
      archive opfile
      stash includes: opfile, name: dbName
    }
  }
}


pipeline {
  agent { label 'ubuntu-s3' }
  options { timestamps() }
  stages {
    stage ('Data Collection') {
      steps {
        // Actually run the steps in parallel - parallel takes a map as an argument, hence the above.
        parallel stepsForParallel,
        failFast: true
      }
    }

    stage ('Update GIThub') {
      steps {
        checkout scm
        unstash dbNames[0]
        unstash dbNames[1]
        unstash dbNames[2]
        archive includes:'*.sql'
        sh 'git add -A; git comment -m "mysql DB updates"; git push'
      }
    }

//    post {
//      always {
//        echo 'One way or another, I have finished'
//        deleteDir() /* clean up our workspace */
//      }
//      success {
//        echo 'I succeeeded!'
//      }
//      unstable {
//        echo 'I am unstable :/'
//      }
//      failure {
//        echo 'I failed :('
//      }
//      changed {
//        echo 'Things were different before...'
//      }
//    }
  }
}
