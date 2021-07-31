
# EKS 설치준비
 aws configure
 eksctl 다운로드
 CloudFormation 으로 VPC등 리소스 생성

 .\eksctl.exe create cluster --vpc-public-subnets subnet-0423f385dc89165e3,subnet-0c8e5a9bd2c6fa13e,subnet-0b36bd56d3be4802c  --name eks-work-cluster --region ap-northeast-2 --version 1.19 --nodegroup-...


 # 
 # ECR 리포지토리 생성 후 ECR로그인
aws ecr get-login-password --region ap-northeast-2 |
>> docker login --username AWS --password-stdin 966445838000.dkr.ecr.ap-northeast-2.amazonaws.com
Login Succeeded

# ECR에 업로드
docker tag docker-react-app_tests 966445838000.dkr.ecr.ap-northeast-2.amazonaws.com/k8sbook/backend-app:1.0.0
docker push 966445838000.dkr.ecr.ap-northeast-2.amazonaws.com/k8sbook/backend-app:1.0.0

