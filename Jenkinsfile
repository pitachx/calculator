pipeline {
    agent any
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage("Compile") {
            steps {
                sh "./gradlew compileJava"
            }
        }
        stage("Unit test") {
            steps {
                sh "./gradlew test"
            }
        }
	stage("Code coverage") {
	    steps {
		sh "./gradlew jacocoTestReport"
		publishHTML (target: [
			reportDir: 'build/reports/jacoco/test/html',
			reportFiles: 'index.html',
			reportName: "JaCoCo Report"
		])
		sh "./gradlew jacocoTestCoverageVerification"
	    }
	}
	stage("Static code analysis") {
	    steps {
		sh "./gradlew checkstyleMain"
		publishHTML (target: [
			reportDir: 'build/reports/checkstyle/',
			reportFiles: 'main.html',
			reportName: "Checkstyle Report"
		])
	    }
	}
	stage("Package") {
	    steps {
		sh "./gradlew build"
	    }
	}
	stage("Start docker") {
	    steps {
		sh "/etc/init.d/docker start"
		sleep 5
	    }
	}
	stage("Docker build") {
	    steps {
		sh "docker build -t pitachx/calculator ."
	    }
	}
	stage("Docker push") {
	    steps {
		sh "cat p.txt | docker login --username pitachx --password-stdin"
		sh "docker push pitachx/calculator"
	    }
	}
	stage("Deploy to staging") {
	    steps {
		sh "docker-compose up -d"
	    }
	}
	stage("Acceptance test") {
	    steps {
		sleep 60
		sh "bash acceptance_test.sh"
	    }
	}
    }
    post {
	always {
	    sh "docker-compose down"
	}
    }
}

