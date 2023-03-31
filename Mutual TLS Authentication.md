# Mutual TLS Authentication

인증서 기반 상호 TLS 인증 방법에 대한 설명

# RestAPI(Flask)

## Server 만들기

python의 Flask를 활용하여 웹 서버 만들기

## Install Flask

```bash
python3 -m pip install flask
```

## Web Server 실행

### 웹 서버 코드

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
	return 'Hello, Flask'

if __name__ == '__main__':
	app.run()
```

### 웹 서버 실행

```bash
python3 web_server.py

===================================
* Serving Flask app "web_server" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

### 웹 서버 동작 확인

```bash
curl http://localhost:5000/

===================================
Hello, Flask!
```

## TLS

전송 계층 보안(Transport Layer Security, TLS)는 기존의 SSL의 표준화된 것으로 통신 과정에서 전송계층 종단간 보안과 데이터 무결성을 확보해주며, 웹 브라우징, 전자 메일, 인스턴트 메신저, voice-over-IP (VoIP) 등에서 활용되고 있다.

### TLS의 3단계 기본 절차

1. 지원 가능한 알고리즘 서로 교환
2. 키 교환, 인증
3. 대칭키 암호로 암호화하고 메시지 인증

* 일반적으로 키 교환과 인증을 위해 RSA 알고리즘을 사용

![Untitled](Mutual%20TLS%20Authentication%20136a3bee50524c999ea5da200b1230ef/Untitled.png)

- 7> Certificate(client→server) : 서버의 인증서가 유효한지 확인하는 과정
    - 인증서 유효여부는 CA 인증서를 통해 확인함
    - CA는 인증서 발급을 관리하는 기관으로 hierarchy 구조로 구성되며, 우리나라의 Root CA는 KISA
    - insecure mode의 경우 인증서 검증을 패스

## Web Server(HTTPS)

### 서버 인증서 생성

```bash
# create private key and certificate
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt
```

### 클라이언트 인증서 생성

```bash
# create private key and certificate
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout client.key -out client.crt
# combine private key and certificate
openssl pkcs12 -export -out client.pfx -inkey client.key -in client.crt
```

### 웹 서버 코드

```python
from flask import Flask
import ssl

app = Flask(__name__)

@app.route('/')
def hello():
	return 'Hello, Flask'

if __name__ == '__main__':
	context = ssl.SSLContext(ssl.PROTOCOL_TLSv1_2)
	context.verify_mode = ssl.CERT_REQUIRED
	context.load_verify_locations('./client.crt')
	context.load_cert_chain('./server.crt', './server.key')
	app.run(ssl_conext=context)
```

### 웹 서버 실행

```bash
python3 web_server.py

=================================
 * Serving Flask app "web_server" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on https://127.0.0.1:5000/ (Press CTRL+C to quit)
```

### 웹 서버 동작 확인

```bash
curl -k --cert client.pfx --cert-type p12 https://localhost:5000/

======================================
Hello, Flask!
```