import java.text.SimpleDateFormat

def gitCreds = 'AWGITTAPP'
def projectName = 'hello-tomcat-project-2'
def branch = "$BRANCH_NAME"
def jobName = jobName
def deploy_Env = null
def environment = null

cleanupWorkspace() {
	  dir('.') {
        deleteDir()
    }
}

def checkout() {
    git url: "https://github.com/nik786/${projectName}.git", branch: "*/${branch}", credentialsId: "${gitCreds}"		
}

def build() {
		dir("${projectName}") {
				sh "mvn clean package"
		}
}

def s3() {
		def dateFormat = new SimpleDateFormat("yyyy-MM-dd-HH-mm")
		def date = dateFormat.format(new Date())
		sh "aws s3 cp /var/lib/jenkins/workspace/${projectName}/target/mavenproject1-1.0-SNAPSHOT.war s3://hello-artifactory/${date}/mavenproject1-1.0-SNAPSHOT.war"
}

def ansible() {
		sh "ansible-playbook /var/lib/jenkins/helo.yml -i /var/lib/jenkins/local.yml"
		currentBuild.result = 'SUCCESS'
}

try{
		node('master') {
				stage('Execute Build'){}
				
				stage('Set Variables') {
						deploy_Env = "$BRANCH_NAME"
						jobName = "$JOB_NAME"
						projectName = 'hello-tomcat-project-2'
						environment = "${deploy_Env}"
				}
		}
	
		node {
				stage('Cleanup workspace') {
						cleanupWorkspace()
				}
				
				stage('Build') {
						build()  
				}
				
				stage('Copy to Artifacts') {
						s3()
				}
				
				stage("Deployment by Ansible") {
						ansible()
				}	
		}
} finally {
		if (currentBuild.result == 'SUCCESS') {
				stage("Announce") {}
		}
}
