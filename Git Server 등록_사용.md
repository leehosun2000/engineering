# Git Server

Git 서버에 대한 설명

# Git 이란?

> 깃(Git)은 컴퓨터 파일의 변경사항을 추적하고 여러 명의 사용자들 간에 해당 파일들의 작업을 조율하기 위한 분산 버전 관리 시스템으로 소스코드 관리에 주로 사용됨
> 

# Git Server 정보

- 서버 IP : 10.120.2.202
- OS : Ubuntu
- Type : VM
- Hypervisor(KVM) : 10.120.2.201
- 계정
    - Git 관리자 계정 : git-admin
    - Git 저장소 계정 : git-repo

# Git 사용자 등록

## 인증키 등록

깃 서버는 id, password 방식이 아닌 ssh 키로 인증하도록 되어 있으며, 사용자마다 인증키를 생성하여 등록해야 한다.

### 인증키 생성 및 복사

개인 PC에서 사용할 RSA 키를 생성한 후 깃 서버의 git-admin 계정으로 공개키를 복사한다.

```bash
#> ssh-keygen
#> cd ~/.ssh
#> scp id_rsa.pub git-admin@10.120.2.202:/home/git-admin/gitolite-admin/keydir/<key name>.pub
## 여기서 <key name>은 사용자 이니셜을 사용한다. ex) jhlee.pub
```

```bash
C:\> ssh-keygen 
C:\> cd C:\users\<계정>\.ssh
C:\users\<계정>\.ssh> scp id_rsa.pub git-admin@10.120.2.202:/home/git-admin/gitolite-admin/keydir/<key name>.pub
## 여기서 <key name>은 사용자 이니셜을 사용한다. ex) jhlee.pub
```

⚠️  windows command에서 ssh-keygen을 지원하지 않는다면 putty 등 다른 프로그램을 사용하여 RSA 키를 생성한다.

⚠️  windows command에서 scp 명령어가 지원되지 않는다면 winSCP 프로그램을 사용하여 공개키 파일을 복사한다.

### Git 서버 : 키 등록

git-admin 계정으로 깃 서버에 접속

공개키 파일 추가 후 commit, push

```bash
#> cd /home/git-admin/gitolite-admin/keydir
#> ls
## 복사한 키가 디렉토리에 복사되었는지 확인
#> git add .
#> git commit -am 'add key'
#> git push
```

# Git 저장소 관리

## 프로젝트 생성

git-admin 계정으로 깃 서버에 접속

```bash
#> cd /home/git-admin/gitolite-admin/conf
#> vim gitolite.conf
```

gitolite.conf 파일 끝에 아래 syntax에 맞춰 추가

```
repo <project name>
	RW+ = <key name>
	R   = @all
```

수정 후 commit, push

```bash
#> git commit -am 'add project'
#> git push
```