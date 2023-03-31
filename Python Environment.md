# Python Environment

Python의 버전 관리 및 가상 환경 관리 방법에 대한 설명 

ubuntu 환경

# pyenv

## pyenv란?

간단한 python 버전 관리 툴

## Install pyenv

```bash
curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```

```bash
# when no curl, install curl
sudo apt-get install curl
```

## 환경변수 설정 (Set Environment Variable)

### .bashrc 끝에 추가 (append below into .bashrc)

```bash
export PYENV_ROOT="$HOME/.pyenv"         # pyenv root directory
export PATH="$PYENV_ROOT/bin:$PATH"      # path 설정
eval "$(pyenv init --path)"              # pyenv 초기화
eval "$(pyenv init -)"     
eval "$(pyenv virtualenv-init -)"        # pyenv virtualenv 초기화
```

## pyenv 사용법 (pyenv usage)

### 현재 버전 확인 (check python versions)

```bash
pyenv versions
python --version
```

![Untitled](Python%20Environment%208be70ca3734f48c498c18afcab80921c/Untitled.png)

### python 설치 (install python)

⚠️  python 설치 전 필수 라이브러리 

`python-setuptools` `libncursesw5-dev libgdbm-dev libc6-dev` `zlib1g-dev libsqlite3-dev tk-dev` `libssl-dev openssl` `libffi-dev` libbz2-dev

```bash
# 설치 가능한 python 버전 확인
pyenv install --list

# 새로운 버전의 python 설치
pyenv install 3.7.5

# 설치 확인
pyenv versions
```

![Untitled](Python%20Environment%208be70ca3734f48c498c18afcab80921c/Untitled%201.png)

### python 버전 변경 (change python version)

```bash
# change version
pyenv local 3.7.5           # local -> shell : apply current shell

# check version
pyenv versions
python --version
```

![Untitled](Python%20Environment%208be70ca3734f48c498c18afcab80921c/Untitled%202.png)

### python 가상환경 생성 (create virtualenv), 삭제

```bash
# 현재 python 버전으로 가상 환경 생성
pyenv virtualenv v1            # pyenv virtualenv <virtualenv name>

# 가상환경 생성 확인
pyenv versions

```

![Untitled](Python%20Environment%208be70ca3734f48c498c18afcab80921c/Untitled%203.png)

```bash
pyenv uninstall 3.7.5/v1
pyenv versions
```

![Untitled](Python%20Environment%208be70ca3734f48c498c18afcab80921c/Untitled%204.png)

### python 가상환경 활성화, 비활성화 (activate or deactivate virturalenv)

```bash
pyenv activate v1
pyenv versions
python --version
```

![Untitled](Python%20Environment%208be70ca3734f48c498c18afcab80921c/Untitled%205.png)

```bash
pyenv deactivate v1
pyenv versions
```

![Untitled](Python%20Environment%208be70ca3734f48c498c18afcab80921c/Untitled%206.png)