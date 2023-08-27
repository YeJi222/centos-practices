## tomcat9.0 설치 및 설정

### 1. tomcat9.0 다운로드   
root 경로에서 
```sh
# wget --no-check-certificate https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.79/bin/apache-tomcat-9.0.79.tar.gz
```

### 2. 압축 풀기
```sh
# tar xvfz apache-tomcat-9.0.79.tar.gz
```

### 3. 설치
```sh
# mv apache-tomcat-9.0.79 /usr/local/poscodx2023/tomcat9.0
# ln -s /usr/local/poscodx2023/tomcat9.0 /usr/local/poscodx2023/tomcat
```

### 4. 포트 확인(/usr/local/poscodx2023/tomcat/conf/server.xml)
```sh

...

    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000"
               />
...

```

### 5. 톰캣 서버 실행
```sh
# /usr/local/poscodx2023/tomcat/bin/catalina.sh start // 실행 명령어 
# ps -ef | grep tomcat // 실행 확인 
```

### 6. 브라우저로 접근 하기
- http://설치서버-IP:8080로 리눅스 웹 브라우저에서 실행
- ifconfig로 확인해보니, ip 주소 192.168.64.9
<img width="674" alt="image" src="https://github.com/YeJi222/centos-practices/assets/70511859/6eb13718-6a86-4ac3-a79c-36aa3eca6b6c">

- http://192.168.64.9:8080 접속 
<img width="785" alt="image" src="https://github.com/YeJi222/centos-practices/assets/70511859/77e0536a-b092-4d8c-b5fb-43a93e8bf3af">

### 7. 톰캣 서버 중지
```sh
# /usr/local/poscodx2023/tomcat/bin/catalina.sh stop
```

### 8. 서비스 등록 하기
  1) [/usr/lib/systemd/system/tomcat.service](https://github.com/poscodx-bitacademy/centos-practices/blob/main/lx/usr/lib/systemd/system/tomcat.service) 파일 생성
  2) 서비스 등록
     ```sh
     # systemctl enable tomcat
     ```
### 9. tomcat 서비스 실행/중지/재실행
```sh
# systemctl start tomcat
# systemctl stop tomcat
# systemctl restart tomcat
```

### 10. tomcat manager 설정
  1) [/usr/local/poscodx2023/tomcat/conf/tomcat-users.xml](https://github.com/poscodx-bitacademy/centos-practices/blob/main/lx/usr/local/poscodx2023/tomcat/conf/tomcat-users.xml) 설정
  2) [/usr/local/poscodx2023/tomcat/webapps/manager/META-INF/context.xml](https://github.com/poscodx-bitacademy/centos-practices/blob/main/lx/usr/local/poscodx2023/tomcat/webapps/manager/META-INF/context.xml) 설정

### 11. tomcat 재시작
```sh
# systemctl stop tomcat
# ps -ef | grep tomcat
# systemctl start tomcat
```
### 12. http://설치서버-IP/manager 에서 접근/인증 하기

