# Jenkins

CI/CD 툴인 Jenkins 사용법에 대한 설명

# Jenkins Server 정보

---

- 서버 IP : 10.120.2.203
- WEB 접속 PORT : 8080
- OS : Ubuntu 16.04.6 LTS
- 서버 접속 계정 : petaon
- 배포 서버 접속 : 10.120.2.103:8080

# 계정 관리

---

> Jenkins의 Admin 계정은 최초 설치 시에 생성이 가능합니다.
> 
- Jenkins admin 계정
    - Admin 계정명 : moadata
    - Admin 암호 : vhzotmxj14()! (포캐스터14()!)
- Jenkins 마스터 계정
    - 마스터 계정명 : jenkins
    - 계정 접속 방법 : sudo su - jenkins -s/bin/bash

## Admin 계정 생성 (젠킨스 최초 설치 시)

> Admin 계정의 암호는 특수기호(예: /, ., !, *…)와 더불어 대/소문자의 영문 및 숫자 문자를 사용하는 것을 권장합니다.
> 

① Jenkins Admin 계정의 **아이디**를 입력합니다.

② Jenkins Admin 계정의 **암호**를 입력합니다.

③ Jenkins Admin 계정의 **이름**을 입력합니다.

④ Jenkins Admin 계정의 **Email Address**을 입력합니다.

⑤ **[Save and Finish]** 버튼을 클릭합니다.

## 젠킨스 사용자 계정 추가

① **Jenkins 관리 → Manage Users**으로 이동합니다.

② 사용자 생성을 선택합니다.

③ 사용자 계정의 **계정명, 암호, 이름, 이메일 주소** 등을 입력한 후 **[Create User]** 클릭합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled.png)

## 사용자 계정 역할 별 권한 부여

① **Role-based Authorization Strategy** 플러그인을 설치합니다.

② **Jenkins 관리 → Manage and Assign Roles → Manage Roles**로 이동합니다.

③ Role to add에 **생성할 Role 명**을 입력한 후 **[Add]** 클릭합니다.

④ Add 한 Role에 부여하고자 하는 권한의 체크 박스를 선택합니다

```
***Overall** ← read / **Job** ← build, cancel, read, workspace*
위 권한만 활성화하면 **Job 빌드**만 가능한 계정이 된다.
```

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%201.png)

## 사용자 계정의 역할 추가

① **Jenkins 관리 → Manage and Assign Roles → Assign Roles**로 이동합니다.

② User/group to add에 Role을 **추가할 계정 명**을 입력한 후 **[Add]** 클릭합니다.

③ 계정에 추가시키고 싶은 Role의 체크박스를 선택하여 권한을 부여합니다

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%202.png)

# 소스 코드 연동

---

## Jenkins - Git 연동

### Step 1) Jenkins서버에서 Git 서버로 SSH 접속이 되는지 확인

```bash
Jenkins:~$ ssh username@domain_address
```

### Step 2) Jenkins 마스터로 로그인 된 상태에서 Git 서버로 SSH 접속

> 계정 접속 방법 : sudo su - jenkins -s/bin/bash
> 

Jenkins 마스터로 로그인 된 상태에서 Git 서버로 SSH 접속할 때

비밀번호 없이 공개키를 사용하여 접속할 수 있어야 합니다. (다른 유저계정 상관x)

① 젠킨스 계정의 홈 디렉터리로 이동 후 **ssh-keygen -t rsa** 명령어를 입력하여 ssh키를 생성합니다.

```bash
Jenkins:~$ ssh-keygen -t rsa
```

**~/.ssh** 디렉터리 안에 **id_rsa(개인키)**와 **id_rsa.pub(공개키)**가 생성됩니다.

② **id_rsa.pub(공개키)**를 Git-server에서 연결하고자 하는 계정에 추가합니다.

```bash
# **id_rsa.pub**(공개키)의 전체 내용을 전부 복사하여
Jenkins:~$ vim ~/.ssh/id_rsa.pub

# Git-server 계정 중, Git 관리자 계정 또는 연결하고자 하는 계정에 복사합니다.
Git-server:~$ vim /home/username/.ssh/authorized_keys
```

③ 키를 저장했다면 다시 SSH 접속을 시도합니다.

- 패스워드를 요구하지 않고 바로 로그인이 되어야 합니다.
만일 처음과 같이 패스워드를 요구한다면 설정을 잘못한 것입니다.

### Step 3) Jenkins 웹에서 Credential 등록

**Dashboard > Credentials > System > Global credentials (unrestricted)**

① 해당 경로로 이동 후, **Add Credentials**를 클릭합니다.

② Kind를 **SSH Username with private key**로 설정합니다.

③ Private Key에 **Enter directly**를 클릭합니다.

④ **Add**를 ****클릭하여 **id_rsa(개인키)**의 내용을 전부 복사하여 붙여넣습니다.

⑤ **ID**와 **Description, Username**은 해당 자격증명을 구별할 수 있도록 적은 후, [**Create]** 클릭합니다.

**단, Passphrase는 SSH키를 생성할 때 설정하였다면 입력해야 합니다.**

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%203.png)

# 프로젝트 관리

---

## Maven 프로젝트 등록

### Step 1) 플러그인 설치 및 새 항목 생성

① 플러그인 관리에서 **Maven Integration Plugin**을 설치합니다.

② 대시보드 왼쪽에 있는 **New Item**을 클릭합니다.

### Step 2) 새 항목 세부 정보 입력

① **Enter an item name**에 만들고자 하는 프로젝트명을 입력합니다.

② **Maven project**를 선택합니다.

③ **[OK]** 버튼을 클릭하여 다음 단계로 넘어갑니다.

### Step 3) 프로젝트 세부 정보 입력

테스트 할 프로젝트의 **Description**을 입력합니다.

### Step 4) Git 프로젝트 연동

① **소스 코드 관리** 탭을 클릭합니다.

② **[Git]** 라디오버튼을 클릭합니다.

③ **Git Repository URL** 입력합니다.

SSH프로토콜 예) ssh://username@domain_address/git_directory/

④ **Credentials**는 ③에 접근 권한이 있는 계정을 선택합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%204.png)

### step 5) Build 설정

① **Build** 탭을 클릭합니다.

② **Root POM**에는 Git디렉터리의 하위부터 pom.xml파일의 경로를 입력합니다.

Git디렉터리 바로 아래에 위치한 경우 pom.xml만 입력합니다.

③ **Goals and options**에는 maven빌드 단계에서 추가로 진행할 명령을 입력합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%205.png)

## Freestyle 프로젝트 등록

### Step 1) 새 항목 생성

① 대시보드 왼쪽에 있는 **New Item**을 클릭합니다.

### Step 2) 새 항목 세부 정보 입력

① **Enter an item name**에 만들고자 하는 프로젝트명을 입력합니다.

② **Freestyle project**를 선택합니다.

③ **[OK]** 클릭하여 다음 단계로 넘어갑니다.

### Step 3) 프로젝트 세부 정보 입력

테스트 할 프로젝트의 **Description**을 입력합니다.

### Step 4) Git 프로젝트 연동

① **소스 코드 관리** 탭을 클릭합니다.

② **[Git]** 라디오버튼을 클릭합니다.

③ **Git Repository URL** 입력합니다.

예) ssh://username@domain_address/git_directory/

④ **Credentials**는 ③에 접근 권한이 있는 계정을 선택합니다.

⑤ 추가적으로 필요한 설정들을 마친 후, **Build now**를 클릭하여 Git연동을 확인합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%204.png)

## 빌드 후 조치 설정

> 프로젝트 빌드 후 배포 또는 백업 등을 설정합니다
> 

---

### 1) **Deploy to container**

① **빌드 후 조치** 탭을 클릭합니다.

② **Deploy war/ear to a container**를 추가합니다.

③ **WAR/EAR files**에 .war파일의 경로를 입력합니다.

④ **Add Container**를 클릭하여 배포 서버의 tomcat 버전을 선택합니다.

⑤ **Credentials**는 /conf/tomcat-users.xml을 참고하여 계정과 패스워드를 생성합니다.

⑥ **Tomcat URL**은 tomcat에 설정한 URL을 입력합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%206.png)

### 2) **Publish Over SSH**

> **Jenkins 관리 → 시스템 설정 → Publish over SSH**
> 

① **추가** 를 클릭하여 SSH Server를 추가합니다.

② **Name**은 원하는 접속 이름을 입력합니다.

③ **Hostname**은 접속할 서버의 SSH hostname 또는 IP address를 입력합니다.

④ **Username**은 접속할 서버의 로그인 계정을 입력합니다.

⑤ **Remote Directory**는 연결할 서버에서 사용하고자 하는 디렉터리를 입력합니다.

tomcat에서 바로 배포를 원한다면 tomcat directory/webapp 디렉터리로 입력합니다.

⑥ **고급**을 클릭합니다.

⑦ **Use password authentication, or use a different key** 을 체크합니다.

⑧ **Passphrase / Password**는 **Username**에 입력한 계정의 비밀번호를 입력합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%207.png)

> **Dashboard → Project Name**
> 

① **빌드 후 조치** 탭을 클릭합니다.

② **Send build artifacts over SSH**를 추가합니다.

③ **Name**은 위에서 추가한 SSH Server 이름을 선택합니다.

④ **Source file**은 빌드 후 전송할 파일을 지정합니다. 전체 파일을 이동하려면 **/*로 입력합니다.

⑤ **Remove prefix**는 Source files에서 지정한 경로의 하위 디렉터리를 삭제합니다.

작업공간/A/B/C/test.war가 있을때 C/test.war로 배포하고 싶다면 여기에 A/B를 입력합니다.

⑥ **Remote directory**는 접속할 서버의 원격지입니다.

SSH 서버 설정에서 설정한 remote directory 이하의 경로 지정

⑦ **Exec command는** SSH Server에서 실행할 명령어/스크립트를 입력합니다.

해당 계정의 홈 디렉터리에서 실행합니다

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%208.png)

# 소스 코드 빌드

---

① Git에 변경된 소스코드를 Commit 합니다.

② 젠킨스 웹에 접속하여 해당 프로젝트 선택 후 **지금 빌드**를 클릭합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%209.png)

```
Console Output에서
Build step 'Deploy war/ear to a container' marked build as failure
에러 발생 시 taomcat이 제대로 작동하고 있는지 확인
```

# 소스 코드 배포

---

## tomcat 설정

① tomcat 디렉터리에 conf/에서 tomcat-user.xml을 찾습니다.

② 아래와 같이 파일들을 수정합니다.

```
tomcat-server:~$ sudo vim /usr/local/tomcat/apache-tomcat-8.5.81/conf/tomcat-users.xml
맨 아래에

<!-- tomcat에 사용할 admin 계정의 id와 password를 수정합니다. -->
<role rolename="admin"/>
<role rolename="admin-gui"/>
<role rolename="admin-script"/>
<role rolename="manager"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="admin" password="admin" roles="admin,manager,admin-gui,admin-script,manager-gui,manager-script,manager-jmx,manager-status"/>
```

```
tomcat-server:~$ sudo vim apache-tomcat-8.5.81/webapps/**host-manager**/META-INF/context.xml
tomcat-server:~$ sudo vim apache-tomcat-8.5.81/webapps/**manager**/META-INF/context.xml

위 두 .xml 파일의 해당 부분을 찾아 주석 처리합니다.
<!-
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->
```

```
톰캣 접속 페이지에서
**[에러]Origin 서버가 대상 리소스를 위한 현재의 representation을 찾지 못했거나, 그것이 존재하는지를 밝히려 하지 않습니다.**
에러 발생 시 taomcat을 재시작합니다.
```

배포를 성공했음에도 톰캣 루트 페이지가 보인다면..

```

tomcat-server:~$ sudo vim /var/lib/tomcat/apache-tomcat-8.5.81/conf/server.xml

<!-- <Host></Host> 안에 이 부분을 추가해야 배포한 페이지가 보인다. -->
<Context path="/" docBase="war파일명"  reloadable="false" >
</Context>
```

# 기타 설정 및 에러 해결

---

## Java JDK 경로 설정

```
java.nio.file.AccessDeniedException: /usr/lib/자바경로/..

빌드 시에 위와 같은 에러가 콘솔에 출력된다면,
해당 자바 디렉터리에 다음과 같은 확인이 필요합니다.

1. 자바가 제대로 설치되어 있는가.
2. 자바 디렉터리가 해당 경로에 존재하는가. (자바 디렉터리의 이동 or 이름 변경)
3. 확인이 끝났다면 JDK 경로를 다시 설정합니다.
```

**Dashboard > Global Tool Configuration**

① JDK installations…를 클릭합니다.

② Name은 사용자가 알아볼 수 있도록 적습니다.

③ JAVA_HOME은 Jenkins 서버가 빌드 시 사용할 Java디렉터리의 경로를 입력합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%2010.png)

## GOlang 빌드

> sudo apt-get install golang
> 

① Jenkins에서 Go plugin을 설치합니다.

② Jenkins 서버에 Go를 설치합니다.

③ Go 빌드가 필요한 프로젝트의 빌드 환경에 Go version을 선택합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%2011.png)

④ Build 단계에서 빌드할 명령어를 작성합니다.

![Untitled](Jenkins%20aa628f837745426d8562a3c97ea73435/Untitled%2012.png)

```
%%% 주의 %%%
빌드하는 과정에서 실패하면 MoAgent_telegraf/plugins/processors/template/template_sample_config.go 파일이 덮어씌워짐
Git에서 다시 받아오거나 수정해줘야 합니다.

%%% 에러 %%%
make[1]: *** [all] Error 
x86_64-w64-mingw32-gcc ←윈도우용 gcc 이 설치되지 않아 발생
sudo apt-get install gcc-mingw-w64
```

# 롤백 또는 특정 시점으로 빌드

---

> Git Parameter plugin을 이용하여 원하는 시점의 커밋으로 빌드
> 

①②③④⑤⑥⑦⑧⑨⑩