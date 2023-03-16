# helpful commands

## 1. 원격 파일 전송

```sh
# local 에서 pem key 로 file.txt 를 원격 서버로 전송하는 명령어  
scp -i /path/to/pem_key file.txt user@remote_server_ip:~/remote_folder/
```



## 2. brew&#x20;

```sh
# install 
brew install mysql

# MySQL 서버 실행하기
mysql.server start

# MySQL 서버 종료하기
mysql.server stop 

# MySQL을 데몬으로 실행하기 (background)
brew services start mysql

# 서비스 재시작하기
brew services restart mysql 

# demon 으로 실행되고 있는 프로그램 목록 확인하기 
brew services list 

# 데몬 형태로 실행되고 있는 MySQL 종료하기
brew services stop mysql
```

