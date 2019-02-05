import groovy.json.JsonOutput
timestamps {
 def deploy_Env = null
 def jobName = null
 def app
 def gitCreds = 'AWGITTAPP'
 def shortGitCommit = null
 def environment = null
 def branch = 'master'
 def BRANCH_NAME = 'master'
      final String STAGING     = "staging"
      final String PRODUCTION  = "production"

 
stage('Execute Build') {
  node('ec2') {
   
stage('Set Variables') {
  node {
   deploy_Env = env.BRANCH_NAME
   jobName = env.JOB_NAME
   projectName = hello-tomcat-project-2
   environment = "${deploy_Env}"
  }
 }  

try {
    stage('Build') {
     stage 'Cleanup'
     deleteDir()

    def checkout() {
        stage 'Checkout code'
        checkout scm
        dir('deploy-test-2') {
        deleteDir()
        git url: "https://github.com/nik786/${projectName}.git", branch: "${branch}", credentialsId: "${gitCreds}"
        def GIT_COMMIT_HASH = sh(script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
        shortGitCommit = GIT_COMMIT_HASH[0..6]
        setBuildStatus ("${context}", 'Checking out completed', 'SUCCESS')
    }

    def build () {
        stage 'Build'
        sh   'mvn clean' 
    }
    def package () {
        stage 'Package'
        sh 'mvn package'
    }  
    def s3 () {
        stage "Copy to Artifacts"
        dt=`date "+%Y-%m-%d-%H-%M"`
        sh aws s3 cp /var/lib/jenkins/workspace/deploy-test/target/mavenproject1-1.0-SNAPSHOT.war s3://hello-artifactory/$dt/mavenproject1-1.0-SNAPSHOT.war
	    currentBuild.result = 'SUCCESS
    }  

    def ansible() {
        stage "Deployment By Ansible"
        ansiblePlaybook \
		    playbook: '/var/lib/jenkins/helo.yml',
		    inventory: '/var/lib/jenkins/local.yml'/*,
		}  
    finally {
        if (currentBuild.result == 'SUCCESS') {
        stage 'Announce'
   
    }
   }
  }
}
}
