node {
  stage('Prepare') {
    git 'https://github.com/RakeshGanapathy/my-app.git'
  }
  stage('Build') {

    def mvnHome = tool name: 'maven3', type: 'maven'
    sh "${mvnHome}/bin/mvn clean package"
    sh 'mv target/myweb*.war target/newapp.war'
    //home path = /usr/share/maven
  }
  //    stage('SonarQube Analysis') {
  // 	        def mvnHome =  tool name: 'maven3', type: 'maven'
  // 	        withSonarQubeEnv('sonar') { 
  // 	          sh "${mvnHome}/bin/mvn sonar:sonar"
  // 	        }
  // 	    }
  stage('Build Image') {
    sh 'docker build -t arkhes/mydemo:0.0.2 .'
  }
  stage('Push Image') {
    // create a credential of type secrettext with docker hub password with id as "dockerPass"
    withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
      sh "docker login -u arkhes -p ${dockerPassword}"
    }
    //docker push username/reponame:tagname
    sh 'docker push arkhes/mydemo:0.0.2'
  }
  //    stage('Nexus Image Push'){
  //    sh "docker login -u admin -p admin123 65.0.181.193:8083"
  //    sh "docker tag saidamo/myweb:0.0.2 65.0.181.193:8083/damo:1.0.0"
  //    sh 'docker push 65.0.181.193:8083/damo:1.0.0'
  //    }
  stage('Remove Container') {
    try {
      sh 'docker rm -f tomcattest'
    } catch (error) {
      echo 'docker image with tomcattest not available'
    }
  }
  stage('Deploy') {
    try {
      // 		if aws cloudformation describe-stacks --stack-name dev-nics-proxyservlet-svc --region us-west-2 &>/dev/null 
      // then
      //     aws cloudformation delete-stack --stack-name dev-nics-proxyservlet-svc
      // else
      //     aws cloudformation create-stack --stack-name dev-nics-proxyservlet-svc --region us-west-2 --template-body file://dev-nics-proxyservlet-cluster.yml --parameters file://dev-nics-proxyservlet-svc-param.json --capabilities "CAPABILITY_IAM" "CAPABILITY_NAMED_IAM"
      // fi
      // 		if (sh "aws cloudformation describe-stacks --stack-name s3bucket --region 'ap-south-1'")
      // 		           echo "started updating"

      sh "aws cloudformation create-stack --stack-name s3bucket --template-body file://s3Bucket.yml --region 'ap-south-1'"
    } catch (error) {
      sh "aws cloudformation update-stack --stack-name s3bucket --template-body file://s3Bucket.yml --region 'ap-south-1'"
    }

    sh 'docker run -d -p 8090:8080 --name tomcattest arkhes/mydemo:0.0.2'
  }
}
