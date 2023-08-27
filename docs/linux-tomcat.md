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

```
[Unit]
Description=tomcat9
After=network.target syslog.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/local/poscodx2023/java

User=root
Group=root

ExecStart=/usr/local/poscodx2023/tomcat/bin/startup.sh
ExecStop=/usr/local/poscodx2023/tomcat/bin/shutdown.sh

UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```
    
  3) 서비스 등록
 ```sh
 # systemctl enable tomcat
 ```
     
### 9. tomcat 서비스 실행/중지/재실행
- 이제 톰캣 실행 및 중지 시킬 때 /usr/local/poscodx2023/tomcat/bin/catalina.sh stop과 같은   
  복잡한 명령어 대신 systemctl을 사용한 간단한 명령어를 사용할 수 있다
- 아래 명령어 실행 후, 브라우저 접근 테스트 해보기 
```sh
# systemctl start tomcat
# systemctl stop tomcat
# systemctl restart tomcat
```

### 10. tomcat manager 설정
  1) [/usr/local/poscodx2023/tomcat/conf/tomcat-users.xml](https://github.com/poscodx-bitacademy/centos-practices/blob/main/lx/usr/local/poscodx2023/tomcat/conf/tomcat-users.xml) 설정

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
<!--
  By default, no user is included in the "manager-gui" role required
  to operate the "/manager/html" web application.  If you wish to use this app,
  you must define such a user - the username and password are arbitrary.

  Built-in Tomcat manager roles:
    - manager-gui    - allows access to the HTML GUI and the status pages
    - manager-script - allows access to the HTTP API and the status pages
    - manager-jmx    - allows access to the JMX proxy and the status pages
    - manager-status - allows access to the status pages only

  The users below are wrapped in a comment and are therefore ignored. If you
  wish to configure one or more of these users for use with the manager web
  application, do not forget to remove the <!.. ..> that surrounds them. You
  will also need to set the passwords to something appropriate.
-->
<!--
  <user username="admin" password="<must-be-changed>" roles="manager-gui"/>
  <user username="robot" password="<must-be-changed>" roles="manager-script"/>
-->
<!--
  The sample user and role entries below are intended for use with the
  examples web application. They are wrapped in a comment and thus are ignored
  when reading this file. If you wish to configure these users for use with the
  examples web application, do not forget to remove the <!.. ..> that surrounds
  them. You will also need to set the passwords to something appropriate.
-->
<!--
  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <user username="tomcat" password="<must-be-changed>" roles="tomcat"/>
  <user username="both" password="<must-be-changed>" roles="tomcat,role1"/>
  <user username="role1" password="<must-be-changed>" roles="role1"/>
-->

  <role rolename="manager"/>
  <role rolename="manager-gui" />
  <role rolename="manager-script" />
  <role rolename="manager-jmx" />
  <role rolename="manager-status" />
  <role rolename="admin"/>
  <user username="admin" password="manager" roles="admin,manager,manager-gui, manager-script, manager-jmx, manager-status"/>
</tomcat-users>  
```
tomcat-users 태그 사이에 삽입
```xml
<tomcat-users>
  . . .
  . . . 
  <role rolename="manager"/>
  <role rolename="manager-gui" />
  <role rolename="manager-script" />
  <role rolename="manager-jmx" />
  <role rolename="manager-status" />
  <role rolename="admin"/>
  <user username="admin" password="manager" roles="admin,manager,manager-gui, manager-script, manager-jmx, manager-status"/>

</tomcat-users>
```
     
  2) [/usr/local/poscodx2023/tomcat/webapps/manager/META-INF/context.xml](https://github.com/poscodx-bitacademy/centos-practices/blob/main/lx/usr/local/poscodx2023/tomcat/webapps/manager/META-INF/context.xml) 설정
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<!--
<Context antiResourceLocking="false" privileged="true" >
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
-->
<Context antiResourceLocking="false" privileged="true" docBase="${catalina.home}/webapps/manager">
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="^.*$" />
</Context>
```

주석 처리
```
<Context>
 ....
</Context>
```
새로 다음내용 추가
```
<Context antiResourceLocking="false" privileged="true" docBase="${catalina.home}/webapps/manager">
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="^.*$" />
</Context>
```

### 11. tomcat 재시작
```sh
# systemctl stop tomcat
# ps -ef | grep tomcat
# systemctl start tomcat
```

### 12. http://설치서버-IP:8080/manager 에서 접근/인증 하기
<img width="776" alt="image" src="https://github.com/YeJi222/centos-practices/assets/70511859/d6f7e823-0cc3-4b27-8add-4e80858fc7c6">

Username : admin
Password : manager
- /usr/local/poscodx2023/tomcat/conf/tomcat-users.xml에서 확인 가능   
<img width="684" alt="image" src="https://github.com/YeJi222/centos-practices/assets/70511859/a1e34f7c-b432-418e-87ee-8b2b1eaa9734">

- 인증 완료 
<img width="785" alt="image" src="https://github.com/YeJi222/centos-practices/assets/70511859/174ba608-7594-4f2f-a0a7-21f52f21b38f">






