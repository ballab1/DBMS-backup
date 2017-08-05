timestamps {
    node('ubuntu-s3') {
        cleanWs()
	stage ('nconf') {
	    withCredentials([usernamePassword(credentialsId: '1a69cdbb-be20-4bde-b30a-87ef9b2db969',
					      passwordVariable: 'PWRD',
					      usernameVariable: 'USER')
			    ]) {
		sh "sudo docker exec -i mysql mysqldump --user ${USER} --password=${PWRD} nconf > nconf.sql"
		stash includes: 'nconf.sql', name: 'nconf'
	     }
	}
	stage ('phpmyadmin') {
	    withCredentials([usernamePassword(credentialsId: '1a69cdbb-be20-4bde-b30a-87ef9b2db969',
					      passwordVariable: 'PWRD',
					      usernameVariable: 'USER')
			    ]) {
		sh "sudo docker exec -i mysql mysqldump --user ${USER} --password=${PWRD} phpmyadmin > phpmyadmin.sql"
		stash includes: 'phpmyadmin.sql', name: 'phpmyadmin'
	     }
	}
	stage ('zen') {
	    withCredentials([usernamePassword(credentialsId: '1a69cdbb-be20-4bde-b30a-87ef9b2db969',
					      passwordVariable: 'PWRD',
					      usernameVariable: 'USER')
			    ]) {
		sh "sudo docker exec -i mysql mysqldump --user ${USER} --password=${PWRD} zen > zen.sql"
		stash includes: 'zen.sql', name: 'zen'
	     }
	}
	stage ('Update GIThub') {
            withCredentials([usernamePassword(credentialsId: '935a7b57-da74-45f7-9119-5a0529afb8ae',
					      passwordVariable: 'GIT_PASSWORD',
					      usernameVariable: 'GIT_USERNAME')
			    ]) {
                sh 'rm -rf *'
		sh 'git clone -v -b master https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ballab1/DBMS-backup.git .'
		unstash 'nconf'
		unstash 'phpmyadmin'
		unstash 'zen'
		archive includes:'*.sql'
		sh 'git add -A'
		sh 'git commit -m "mysql DB updates"'
		sh 'git push -v'
	     }
	}
    }
}
