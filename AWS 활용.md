# AWS EC2 서버 생성후 수행할 내용
 java 설치
sudo yum install -y yava-1.8.0-openjdk-devel.x86_64
sudo /usr/sbin/alternatives --config java

# 한국시간으로 변경
]$ sudo rm /etc/localtime
]$ sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime


# 서버 Hostname 변경
]$ sudo vim /etc/sysconfig/network

아래 항목으로 이름 추가
HOSTNAME=webserver

저장후 sudo reboot


# 로컬 파일 아마존 S3에 백업
 1. AWS CLI 설치
   https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-windows.html#cliv2-windows-install
 2. aws configure : IAM에서 생성한 사용자 정보 입력
 3. 백업실행
$ aws s3 sync ./dev-tips/ s3://buckettest77/backup_test
