import groovy.json.JsonOutput

timestamps {
 def deploy_Env = null
 def jobName = null
 def app
 def gitCreds = 'AWGITTAPP'
 // def gitCreds =  '32f2c3c2-c19e-431a-b421-a4376fce1186'
 def shortGitCommit = null
 def environment = null
 def branch = 'master'
 def BRANCH_NAME = 'master'

      final String STAGING     = "staging"
      final String PRODUCTION  = "production"

 
stage('Set Variables') {
  node {
   //echo sh(returnStdout: true, script: 'env')
   deploy_Env = env.BRANCH_NAME
   jobName = env.JOB_NAME
   projectName = jobName
   environment = "${deploy_Env}"
  }
 }  

try {
    stage('maven Build') {
     
     dir('deploy-test') {
      //deleteDir()
      git url: "https://github.com/nik786/hello-tomcat-project-2", branch: "${branch}", credentialsId: "${gitCreds}"
	  sh 'printenv'
      def GIT_COMMIT_HASH = sh(script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
      shortGitCommit = GIT_COMMIT_HASH[0..6]
      

      stage("push to s3") {
	      dt=`date "+%Y-%m-%d-%H-%M"`
       sh aws s3 cp /var/lib/jenkins/workspace/deploy-test/target/mavenproject1-1.0-SNAPSHOT.war s3://hello-artifactory/$dt/mavenproject1-1.0-SNAPSHOT.war
	      currentBuild.result = 'SUCCESS'
       
       }
      }
}
  }

 stage('execute smoke test'){
		    ansiblePlaybook \
		    playbook: '/etc/ansible/helo.yml',
		    inventory: '/etc/ansible/local.yml'/*,
			//  extraVars: [
			//	  ansible_ssh_user: "$SSH_USR",
				//  ansible_ssh_pass: "$SSH_PSW",
				//  ansible_become_pass: "$SSH_PSW",
				//  current_directory: "$WORKSPACE"
			 // ]*/
     //   }

   
 finally {
    if (currentBuild.result == 'SUCCESS') {
     stage 'Announce'
   
    }
   }
  }
}
}
