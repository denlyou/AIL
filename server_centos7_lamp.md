# Server

## CentOS7 LAMP 스택 구축

- apache 대신에 nginx 로 구축

> [참고 : DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-centos-7)

### nginx 설치

```shell
sudo yum install epel-release
sudo yum install nginx
# 설치 확인
sudo nginx -v
# 시작 & 자동시작
sudo systemctl start nginx
sudo systemctl enable nginx
```

### MariaDB 설치 (Ver 10.2.x)
- [저장소 업데이트](https://downloads.mariadb.org/mariadb/repositories/#mirror=kaist&distro=CentOS&distro_release=centos7-amd64--centos7&version=10.2)

  - `sudo vim /etc/yum.repos.d/MariaDB.repo`

```shell
# MariaDB 10.2 CentOS repository list - created 2017-08-28 05:48 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

```shell
sudo yum install MariaDB-server MariaDB-client
mysql -V
# 초기 세팅
mysql_secure_installation
# 시작 & 자동시작
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

### php 설치 (Ver 7.1.x)

```shell
yum install -y epel-release
rpm -ivh http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum --enablerepo=remi update remi-release
yum --enablerepo=remi-php71 install -y php php-fpm php-mysql php-gd php-mbstring php-xml
# 설치 확인
php -v
```

- php 설정

```shell
sudo vim /etc/php.ini
# 설정 변경값
cgi.fix_pathinfo = 0
```

- nginx 연동을 위한 fpm 설정

```shell
sudo vim /etc/php-fpm.d/www.conf
# 리스너 변경 (소켓 형식)
listen = /var/run/php-fpm/php-fpm.sock
# 유저,그룹 변경 (초기에 apache로 되어 있음)
user = nginx
group = nginx
# 앞에 주석 ; 을 지움
listen.owner = nginx
listen.group = nginx
```

- fpm 시작 & 자동 시작

```shell
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
```

### nginx와 php연동

- nginx 설정 변겅
> - /etc/nginx/conf.d/default.conf 파일이 없는 경우
>   - /etc/nginx/nginx.conf 파일에서 80포트 서버 설정 주석처리 후
>   - /etc/nginx/conf.d/default.conf 만들어 내용 복사 설정

```shell
sudo vim /etc/nginx/conf.d/default.conf
# default.conf 설정
server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        index index.html index.htm;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }

}
```

- php 연동 설정 추가

```shell
server {
        listen       80;
        listen       [::]:80;
        server_name  your_domain.com;
        root         /usr/share/nginx/html;

        index index.php index.html;
        # index.php 파일을 처리하도록 추가

        location / {
                # 404 설정 추가
                try_files $uri $uri/ =404;
                # 아래는 laravel사용시 url 설정
                # try_files $uri $uri/ /index.php?$query_string;

        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }

        # .php 확장자를 가진 파일은 php 처리하도록 (php-fpm연동)
        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }

}
```

#### 확인용 phpinfo() 호출 파일

- `sudo vim /usr/share/nginx/html/info.php`

```php
<?php phpinfo(); ?>
```

#### phpMyAdmin 설치
- [다운로드](https://www.phpmyadmin.net/downloads/)

```shell
sudo yum install wget
# 위는 wget이 설치 안된 경우만
sudo wget https://files.phpmyadmin.net/phpMyAdmin/4.7.4/phpMyAdmin-4.7.4-all-languages.tar.gz
sudo tar -xvf phpMyAdmin-4.7.4-all-languages.tar.gz
sudo mv phpMyAdmin-4.7.4-all-languages myAdmin
sudo chown nginx:nginx -R myAdmin
```

> - session이 권한이 없을수도 있음

```shell
sudo chown nginx:nginx -R /var/lib/php/session
```

---

> 참고
> - [라라벨 설치](./php_laravel_installation.md)
