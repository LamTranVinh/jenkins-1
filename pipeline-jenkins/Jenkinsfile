node {
    def commit_it
    stage('Preparation') {
	checkout scm
	sh "git rev-parse --short HEAD > .git/commit-id"
	commit_id = readfile('.git/commit-id').trim()
    }

    stage('test') {
	def myTestContainer = doceker.image('node:4.6')
	myTestContainer.pull()
	myTestContainer.inside {
	  sh 'npm install --only=dev'
	  sh 'npm test'
	}
    }

    stage('test with a DB') {
	def mysql = docker.image('mysql').run("-e MYSQL_ALLOW_EMPTY_PASSWORD=YES --rm")
	def myTestContainer = docker.image('node:4.6')
	myTestContainer.pull()
	myTestContainer.inside("--link ${mysql.id}:mysql") {
	  sh 'npm install --only=dev'
	  sh 'npm test'
	}
    }

    stage('docker build/push') {
	docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
	  def app = docker.build("lamtranvinh/demovinh:${commit_id}", '.').push()
	}
     }	
}
