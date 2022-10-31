node {
    withDockerContainer('python:2-alpine') {
        stage('Build') {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    withDockerContainer('qnib/pytest') {
        stage('Test') {
            checkout scm
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    stage('Deploy') {
        input message: 'Lanjutkan ke tahap Deploy?'
        checkout scm
        sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-ariecc/sources:/src cdrx/pyinstaller-linux:python2 \'pyinstaller -F add2vals.py\''
        archiveArtifacts artifacts: 'sources/add2vals.py', followSymlinks: false
	withCredentials ([gitUsernamePassword(credentialsId: '133c1b63-70fa-4263-a7bf-42109f8ea02d', gitToolName: 'Default')]) {
    		sh 'git push https://git.heroku.com/spooky-fangs-33418.git HEAD:refs/heads/master -f'
	}	    
        sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-ariecc/sources:/src cdrx/pyinstaller-linux:python2 \'rm -rf build dist\''
	sleep time: 1, unit: 'MINUTES'
    }
}
