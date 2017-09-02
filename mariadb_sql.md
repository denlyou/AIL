#  MariaDB

> MySQL 호환

## SQL

### 유저 관리

- 새로운 유저 생성 : `CREATE USER '유저명'@'접속허용범위' IDENTIFIED BY '패스워드';`

- 테이블 관리 권한 : `GRANT all privileges ON DB명.테이블명 TO '유저명'@'접속허용범위' IDENTIFIED BY '패스워드';`

- 변경사항 저장 : `flush privileges;`

```sql
create user 'userid'@'%' identified by 'password';
grant all privileges on dbname.* to 'userid'@'%' identified by 'password';
flush privileges;  
```

### [트렌잭션](https://mariadb.com/kb/en/mariadb/start-transaction/)

```sql
SET autocommit=0;
START TRANSACTION;
COMMIT;
ROLLBACK;
SET autocommit=1;
```

### Advance

#### 시간 필드(datetime) 기준으로 10분단위로 그룹

```sql
SELECT *, MAX(`datetime`) as _alias
  FROM 테이블명
 WHERE 1
GROUP BY MONTH( datetime ) , DAYOFMONTH( datetime ) , HOUR( datetime ) , FLOOR( MINUTE( datetime ) /10 )
ORDER BY _dt DESC ;
```
