node {
  stage('Prepare') {
    git 'https://github.com/RakeshGanapathy/ECSAutomation.git'
  }

  stage('Build') {
    def mvnHome = tool name: 'maven3', type: 'maven'
    sh "${mvnHome}/bin/mvn clean package"
    sh 'mv target/myweb*.war target/newapp.war'
    //home path = /usr/share/maven
  }

  stage('Build Image') {
    sh 'docker build -t pfm-pro .' 
    sh 'docker tag pfm-pro:latest 964826016001.dkr.ecr.ap-south-1.amazonaws.com/pfm-pro:latest'
  }

  stage('Push Image') {
    sh 'docker login -u AWS -p $(aws ecr get-login-password --region ap-south-1) 964826016001.dkr.ecr.ap-south-1.amazonaws.com'
     try {
      sh 'aws ecr create-repository --repository-name pfm-pro --region ap-south-1'
    } catch (error) {
      echo 'ecr repo already persist'
    }
    
    sh 'docker push 964826016001.dkr.ecr.ap-south-1.amazonaws.com/pfm-pro:latest'
  }


  stage('Deploy') {
    try {
      if ! sh aws cloudformation describe-stacks --stack-name ecs-fargate --region 'ap-south-1'  ; then
        sh "aws cloudformation create-stack --stack-name ecs-fargate --template-body file://CloudFormation//ecs-fargate.yml --capabilities CAPABILITY_NAMED_IAM --region 'ap-south-1'"
      
      else 
        echo -e "\nStack exists, attempting update ..."
        sh "aws cloudformation update-stack --region 'ap-south-1' --stack-name ecs-fargate --capabilities CAPABILITY_NAMED_IAM"

    
    echo "Waiting for stack update to complete ..."
    sh "aws cloudformation wait stack-update-complete --region 'ap-south-1' --stack-name ecs-fargate "

    } catch (error) {
        echo error
        sh "aws cloudformation update-stack --stack-name ecs-fargate --template-body file://CloudFormation//ecs-fargate.yml --capabilities CAPABILITY_NAMED_IAM --region 'ap-south-1'"
    }
  }
}