node {
  stage('Prepare') {
    git 'https://github.com/RakeshGanapathy/ecs-ecr.git'
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
    sh 'aws ecr create-repository --repository-name pfm-pro --image-tag-mutability IMMUTABLE'
    sh 'docker push 964826016001.dkr.ecr.ap-south-1.amazonaws.com/pfm-pro:latest'
  }

  stage('Deploy') {
    try {
      sh "aws cloudformation create-stack --stack-name ecs-faragte --template-body file://CloudFormation//ecs-fargate.yml --region 'ap-south-1'"
    } catch (error) {
      sh "aws cloudformation update-stack --stack-name ecs-faragte --template-body file://CloudFormation//ecs-fargate.yml --region 'ap-south-1'"
    }
  }
}