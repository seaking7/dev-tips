
# Maria DB 설치
윈도우 설치시 cmd에서는 되는데 git bash에서는 mysql 명령어가 안먹음..


# Ubuntu 에 Mariadb 설치
sudo apt-get update
sudo apt install mariadb-server
sudo apt-get install mariadb-client

-- root 패스워드 설정 등
sudo mysql_secure_installation

-- db 접속
sudo mysql -u root -p

create user 'taekyung'@'%' identified by '패스워드';

[mysql]> grant all privileges on mysql to taekyung@'%';
Query OK, 0 rows affected (0.000 sec)

[mysql]> flush privileges;
Query OK, 0 rows affected (0.000 sec)



# RDS사용시 활용
- charset이 디폴트값이 latin 이므로, 파라미터그룹을 하나 생성해서 utf8로 변경필요
