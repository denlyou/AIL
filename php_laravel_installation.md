# PHP

## Laravel

### Linux(CentOS/Ubuntu)에서 [설치하기](https://laravel.com/docs/5.5#installation)

#### 체크 사항

- PHP >= 7.0.0
- OpenSSL PHP Extension
- PDO PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension
- XML PHP Extension

#### [composer 설치 (php 패키지 관리자)](https://getcomposer.org/doc/00-intro.md)

```shell
# php-cli를 이용해 설치
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
# 모둔 유저가 사용할 수 있도록 이동
sudo mv composer.phar /usr/local/bin/composer
# 설치 확인 및 버전 확인
composer -V
```

##### laravel installer도구 설치

```shell
composer global require "laravel/installer"
```
> 공식 문서에는 `~/.composer/vendor/bin`에 설치된다고 하는데  `~/.config/composer/vendor/bin`에 설치됨

> `~/.bash_profile`등의 파일에서 PATH 환경 변수에 `$HOME/.config/composer/vendor/bin` 추가

```shell
# ~/.bash_profile 파일 설정의 예
PATH=$PATH:[기존설정]:$HOME/.config/composer/vendor/bin
```

#### 설치도구가 안될 때

```shell
# 주의 composer는 root(sudo)로 실행하지 말것!
composer create-project --prefer-dist laravel/laravel [프로젝트명]
```

#### 권한 설정 storage, bootstrap/cache 폴더

```shell
sudo chmod 777 -R storage
sudo chmod 777 -R bootstrap/cache
```

#### uri 설정 (Pretty URLs / rewrite 모듈)

- nginx

```shell
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

- apache2

```shell
Options +FollowSymLinks
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [L]
```
