## AWS Session Manager란
- [ ] Session Manager는 대화형 원클릭 브라우저 기반 셸 또는 AWS CLI를 통해 Amazon EC2 인스턴스를 관리할 수 있는 AWS Systems Manager의 기능입니다. Session Manager를 사용하여 계정의 인스턴스에 세션을 시작할 수 있습니다. 세션이 시작된 후, 다른 연결 유형을 통해 bash 명령을 실행할 수 있습니다.
    - 관리형 노드에 대한 중앙 집중식 액세스 제어
    - 세션 활동 로깅 및 감사
    - 불필요한 인바운드 포트를 제거 

***

### launch Template UserData

 - EFS_FS_ID : 이전 랩에서생성한 EFS ID 기입
 - DB_NAME : Wordpress 데이터가 저장될 DB로 LAB2 에서 생성한 DB 이름 기입
 - DB_HOST : database Writer instance의 url 입력
    ex) wordpress-workshop.cluster-ctdnyvvewl6s.eu-west-1.rds.amazonaws.com.

 - DB_USERNAME : database username 
 - DB_PASSWORD 


```
#!/bin/bash

DB_NAME="wordpress"
DB_USERNAME="wpadmin"
DB_PASSWORD=""
DB_HOST="wordpress-workshop.cluster-xxxxxxxxxx.eu-west-1.rds.amazonaws.com"
EFS_FS_ID="fs-xxxxxxxxx"

dnf update -y

#install wget, apache server, php and efs utils
dnf install -y httpd wget php-fpm php-mysqli php-json php amazon-efs-utils

#create wp-content mountpoint
mkdir -p /var/www/html/wp-content
mount -t efs $EFS_FS_ID:/ /var/www/html/wp-content

#install wordpress
cd /var/www
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
rm -f latest.tar.gz

#change wp-config with DB details
cp -rn wordpress/* /var/www/html/
sed -i "s/database_name_here/$DB_NAME/g" /var/www/html/wp-config.php
sed -i "s/username_here/$DB_USERNAME/g" /var/www/html/wp-config.php
sed -i "s/password_here/$DB_PASSWORD/g" /var/www/html/wp-config.php
sed -i "s/localhost/$DB_HOST/g" /var/www/html/wp-config.php

#change httpd.conf file to allowoverride
#  enable .htaccess files in Apache config using sed command
sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# create phpinfo file
echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php

# Recursively change OWNER of directory /var/www and all its contents
chown -R apache:apache /var/www

systemctl restart httpd
systemctl enable httpd


```

### EFS 구성 확인
```
aws efs describe-mount-targets --file-system-id fs-12345678
```

### VPC DNS 설정
```
aws ec2 modify-vpc-attribute --vpc-id vpc-ID --enable-dns-support
aws ec2 modify-vpc-attribute --vpc-id vpc-ID --enable-dns-hostnames

```

### MYSQL 설치
```
# Amazon Linux 2의 경우 MySQL 공식 리포지토리를 추가
sudo rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm

# MySQL 클라이언트 설치
sudo yum install mysql-community-client -y

```