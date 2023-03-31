# Jupyterhub

Jupyterhub에 대한 설명

# Jupyterhub

Jupyterhub는 멀티 사용자를 위한 Jupyter notebook 버전으로 기본적으로 시스템 계정을 통해 인증 후 접근할 수 있다.

## Installation

Jupyterhub 설치에 대한 가이드

### Install nodejs, npm

```bash
sudo apt-get install -y nodejs npm
```

### install jupyterhub

```bash
mkdir jupyterhub
cd jupyterhub
sudo python3 -m pip install jupyterhub
sudo python3 -m pip install jupyterlab notebook
```

⚠️  여기서 시스템 계정에 대한 접근이 필요하기 때문에 sudo 권한으로 설치를 권장함

      sudo 권한으로 설치하지 않으면, 설치 시스템 계정은 정상 로그인되나 다른 계정에서는 인증이 실패됨

### install http-proxy

```bash
sudo npm install -g configurable-http-proxy
```

### check installed packages

```bash
jupyterhub -h
configurable-http-proxy -h
```

### make scripts

```bash
#!/bin/sh
##########################################
#############    SET ENV    ##############
##########################################
PRG="$0"
PRG_ID="${0##*/}"
PRG_NM=`echo ${PRG_ID} |cut -d. -f1`

while [ -h "$PRG" ]; do
	ls=`ls -ld "$PRG"`
	link=`expr "$ls" : '.*-> \(.*\)$'`
	if expr "$link" : '.*/.*' > /dev/null; then
		PRG="$link"
	else
		PRG=`dirname "$PRG"`/"$link"
	fi
done

PRG_DIR=`dirname "$PRG"`

[ -z "$PRG_HOME" ] && PRG_HOME=`cd "$PRG_DIR" ; pwd`

RED='\e[31m'
NC='\e[39m'
GREEN='\e[32m'
USER_PASS="vhzotmxj14()!"
##########################################
#############    END ENV    ##############
##########################################

# check ip address
IP_ADDR=`hostname -I | awk '{print $1}' |head -1`

# Check jupyterhub process
PID=`ps -ef |grep jupyterhub |grep -v grep |awk '{print $2}'`
if [ "$PID" = "" ]; then
    echo -e "${GREEN}START jupyterhub...${NC}"
    echo "'$USER_PASS'" | sudo -S nohup jupyterhub --ip $IP_ADDR > /dev/null &

    sleep 2
    # Check process again
    rPID=`ps -ef |grep jupyterhub |grep -v grep |awk '{print $2}'`
    if [ "$rPID" = "" ]; then
        echo -e "${RED}[FAIL] Start jupyterhub${NC}"
        exit 1
    else
        echo -e "${GREEN}[START] jupyterhub($rPID) OK!${NC}"
    fi
else
    echo -e "${RED}[ERROR] ${GREEN}jupyterhub($PID) is already running...${NC}"
    exit 0
fi
exit 0
```

```bash
#!/bin/sh
##########################################
#############    SET ENV    ##############
##########################################
PRG="$0"
PRG_ID="${0##*/}"
PRG_NM=`echo ${PRG_ID} |cut -d. -f1`

while [ -h "$PRG" ]; do
    ls=`ls -ld "$PRG"`
    link=`expr "$ls" : '.*-> \(.*\)$'`
    if expr "$link" : '.*/.*' > /dev/null; then
        PRG="$link"
    else
        PRG=`dirname "$PRG"`/"$link"
    fi
done

PRG_DIR=`dirname "$PRG"`

[ -z "$PRG_HOME" ] && PRG_HOME=`cd "$PRG_DIR/.." ; pwd`

RED='\e[31m'
NC='\e[39m'
GREEN='\e[32m'
USER_PASS="vhzotmxj14()!"
##########################################
#############    END ENV    ##############
##########################################
# Check jupyterhub process
PID=`ps -ef |grep jupyterhub |grep -v grep |awk '{print $2}' |head -1`
if [ "$PID" = "" ]; then
    echo -e "${RED}[ERROR] jupyterhub is already not running...${NC}"
    exit 1
else
    echo -e "Stopping jupyterhub..."
    echo "$USER_PASS" | sudo -S kill `ps -ef |grep jupyterhub |grep -v grep |awk '{print $2}'`

    sleep 5

    # Check process again
    rPID=`ps -ef |grep jupyterhub |grep -v grep |awk '{print $2}' |head -1`
    if [ "$rPID" = "" ]; then
        echo -e "${GREEN}[OK] Stop jupyterhub(${PID}) successfully${NC}"
    else
        echo -e "${RED}[ERROR] FAIL to stop jupyterhub${NC}"
        exit 1
    fi
fi
exit 0
```

## Jupyterhub 실행

### 실행

```bash
cd /home/{user}/jupyterhub
sh start.sh
```

![Untitled](Jupyterhub%20b49799b1e712459bbe44e6f5a61c2c03/Untitled.png)

![Untitled](Jupyterhub%20b49799b1e712459bbe44e6f5a61c2c03/Untitled%201.png)

### 중지

```bash
cd /home/{user}/jupyterhub
sh stop.sh
```

## virtualenv 연동

### install packages

```bash
sudo apt-get install libffi-dev libsqlite3-dev
```

* pyenv install 3.9.1

### 가상환경 활성화

```bash
pyenv activate dev1
```

### ipykernel 설치

```bash
pip install ipykernel
```

### ipykernel 추가

```bash
python -m ipykernel install --user --name <virtualenv name> --display-name <display name at jupyterhub>
```

![Untitled](Jupyterhub%20b49799b1e712459bbe44e6f5a61c2c03/Untitled%202.png)

### ipykernel 삭제

```bash
jupyter kerelspec uninstall <virtualenv name>
```

![Untitled](Jupyterhub%20b49799b1e712459bbe44e6f5a61c2c03/Untitled%203.png)