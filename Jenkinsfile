node('test') {  
    def app
    stage('Cloning Git') {
        // Let's make sure we have the repository cloned to our workspace
        checkout scm
    }  
    stage('SAST') {
        // Ensure the job 'SECURITY-SAST-SNYK' exists in Jenkins
        build job: 'SECURITY-SAST-SNYK', propagate: true
    }
    stage('Build-and-Tag') {
        // This builds the actual image; synonymous to docker build on the command line
        app = docker.build("kailasv678/test")
    }
    stage('Post-to-dockerhub') {
        docker.withRegistry('https://registry.hub.docker.com', 'training_creds') {
            app.push("latest")
        }
    }
    stage('SECURITY-IMAGE-SCANNER') {
        // Ensure the job 'SECURITY-IMAGE-SCANNER-AQUAMICROSCANNER' exists in Jenkins
        build job: 'SECURITY-IMAGE-SCANNER-AQUAMICROSCANNER', propagate: true
    }
    stage('Pull-image-server') {
        sh "docker-compose down"
        sh "docker-compose up -d"
    }
    stage('DAST') {
        // Ensure the job 'SECURITY-DAST-OWASP_ZAP' exists in Jenkins
        build job: 'SECURITY-DAST-OWASP_ZAP', propagate: true
    }
}
