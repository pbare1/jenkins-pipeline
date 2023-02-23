node{
    stage('Git Checkout'){
    	git branch: 'dev', url: 'https://github.com/javahometech/my-app'
	}
    stage('Build package Maven'){
    	sh 'mvn clean package '
	}
    stage('Build Docker Image'){
    	sh "docker build -t pbare1/some-web-app ."
	}
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'DockerHubPasko', variable: 'dockerHubPwd')]) {
                sh "docker login -u pbare1 -p ${dockerHubPwd}"
    	}
    	sh 'docker push pbare1/some-web-app'
	}
    stage('Deploy and Run Container on App Server'){
    	def dockerRun = 'docker run -d -p 8904:8080 --name demo-java-app pbare1/some-web-app'
        sshagent(['PrivateKeyHost24']) {
    	    sh "ssh -o StrictHostKeyChecking=no debian@172.26.160.137 'docker stop demo-java-app'"
    	    sh "ssh -o StrictHostKeyChecking=no debian@172.26.160.137 'docker rm demo-java-app'"
    	    sh "ssh -o StrictHostKeyChecking=no debian@172.26.160.137 'docker rmi pbare1/some-web-app'"
            sh "ssh -o StrictHostKeyChecking=no debian@172.26.160.137 ${dockerRun}"
        }	
	}
        stage('e-mail Notification'){
        emailext attachLog: true, attachmentsPattern: 'Jenkinsfile', body: 'This is Email notification fo ripeline job.', recipientProviders: [developers()], subject: 'TEST S4 Pipeline', to: 'pasko.bare@globallogic.com'
        	
    	}
}
