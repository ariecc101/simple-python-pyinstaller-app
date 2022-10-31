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
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }    
    stage('Deploy') {
        checkout scm
        sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-ariecc/sources:/src cdrx/pyinstaller-linux:python2 \'pyinstaller -F add2vals.py\''
        archiveArtifacts artifacts: 'sources/add2vals.py', followSymlinks: false
	withCredentials([gitUsernamePassword(credentialsId: '1a0bfe1b-110e-4e0f-b85d-6ea8434c2171', gitToolName: 'Default')]) {
    	    sh 'git push https://git.heroku.com/spooky-fangs-33418.git HEAD:refs/heads/master -f'
	}
        sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-ariecc/sources:/src cdrx/pyinstaller-linux:python2 \'rm -rf build dist\''
	sleep time: 1, unit: 'MINUTES'
	}
}
