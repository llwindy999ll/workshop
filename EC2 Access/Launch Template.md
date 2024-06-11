
## launch Template UserData

 - EFS_FS_ID : 이전 랩에서생성한 EFS ID 기입
 - DB_NAME : "wordpress" 데이터가 저장될 DB로 LAB2 에서 생성한 DB 이름 기입
 - DB_HOST : database Writer instance의 url 입력
    ex) "wordpress-workshop.cluster-xxxxxxxxxx.~"

 - DB_USERNAME : "wpadmin"
 - DB_PASSWORD : "xxxx"


    ```
    #!/bin/bash

    DB_NAME="wordpress"
    DB_USERNAME="wpadmin"

    DB_PASSWORD="xxxxx"
    DB_HOST="wordpress-workshop.cluster-xxxxxxxxxx.~"
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


    # stress tool 설치
    dnf install -y stress

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