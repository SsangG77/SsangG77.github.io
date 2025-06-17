---
title: GCP VM에 NodeJS https 서버 구성하기
date: 2024-12-08 12:00:00 +0900
categories: [가이드]
tags: [GCP, NodeJS]
---



### 1. AWS에서 도메인 등록하기

1. AWS Route 53 콘솔로 이동
2. 도메인 등록
    1. ‘도메인 등록’ 메뉴로 이동하여 원하는 도메인을 검색하고 등록
    2. 도메인을 등록하면 Route 53에서 자동으로 호스팅 영역이 생성됨

### 2. GCP에서 VM 인스턴스 생성 및 설정

1. GCP 콘솔로 이동
2. 왼쪽 메뉴에서 Compute Engine → VM 인스턴스 만들기 클릭
3. 설정
    1. 이름 : (원하는 이름)
    2. 리전 : (서버 위치)
    3. 영역 : 모두
    4. 부팅 디스크 : (Ubuntu, Debian)
    5. 방화벽 : HTTP 및 HTTPS 트래픽 허용 체크(없으면 인스턴스를 생성하고 수정을 할때 방화벽 설정 가능)
4. 고정 IP 설정

### 3. AWS에 등록한 도메인과 GCP IP 연결

1. 레코드 추가
    1. 레코드 이름 : (비워놓기)
    2. 레코드 유형 : A
    3. 값 : GCP 고정 IP 주소
    4. 라우팅 정책 : 단순 라우팅
2. DNS 전파 확인
    1. AWS에 아까 등록한 도메인으로 ping 날리기
        
        ```bash
        ping your-domain.com
        ```
        
    2. ping 결과(이 문자가 반복해서 나타남)
        
        ```bash
        64 bytes from 34.22.72.6: icmp_seq=2 ttl=54 time=63.042 ms
        ```
        

### 4. GCP VM에서 Node.js 서버 구성

1. GCP Compute Engine → VM 인스턴스에서 해당 인스턴스 SSH접속
    1. SSH 클릭
        
       ![Image](https://github.com/user-attachments/assets/84bed53c-1339-48d3-a542-3f68ec9a9e00)
        
2. Node.js 및 npm 설치
    
    ```bash
    sudo apt update
    sudo apt install -y nodejs npm
    ```
    
3. 프로젝트 디렉토리 생성(프로젝트 이름은 마음대로)
    
    ```bash
    mkdir ~/my-node-project
    cd my-node-project
    ```
    

### 5. Let's Encrypt로 HTTPS 설정(SSL 인증서)

1. Certbot 설치
    
    ```bash
    sudo apt update
    sudo apt install -y certbot
    ```
    
2. SSL 인증서 발급
    1. 성공 시 `/etc/letsencrypt/live/your-domain.com/`에 인증서가 저장됨
    
    ```bash
    sudo certbot certonly --standalone -d your-domain.com
    ```
    
3. js 파일 만들기
    1. “nano”는 파일을 만들거나 수정하는 명령어다.
    
    ```bash
    nano index.js
    ```
    
4. 기본 HTTPS 서버 코드 작성
    
    ```jsx
    const fs = require('fs');
    const https = require('https');
    const express = require('express');
    
    const app = express();
    const PORT = 80;
    const HTTPS_PORT = 443;
    
    const options = {
        key: fs.readFileSync('/etc/letsencrypt/live/your-domain.com/privkey.pem'),
        cert: fs.readFileSync('/etc/letsencrypt/live/your-domain.com/fullchain.pem'),
    };
    
    app.get('/', (req, res) => {
        res.send('Hello, HTTPS World!');
    });
    
    const http = require('http');
    http.createServer((req, res) => {
        res.writeHead(301, { Location: `https://${req.headers.host}${req.url}` });
        res.end();
    }).listen(PORT, () => {
        console.log(`HTTP server running on port ${PORT}, redirecting to HTTPS.`);
    });
    
    https.createServer(options, app).listen(HTTPS_PORT, () => {
        console.log(`HTTPS server running on port ${HTTPS_PORT}`);
    });
    
    ```
    
5. 서버 실행
    
    ```bash
    sudo node index.js
    ```
    
6. 브라우저에서 테스트
    1. https:///your-domain.com 으로 접속하여 동작 확인

<aside>
💡

Certbot이란

**Certbot**은 **Let's Encrypt 인증서를 쉽게 발급 및 관리할 수 있는 CLI 도구(Command Line Interface Tool)**입니다.

### **Certbot의 역할**

1. 도메인의 소유권을 자동으로 확인합니다(ACME 프로토콜 사용).
2. 인증서를 발급받고, 서버에 적용합니다.
3. 인증서 만료 전에 자동으로 갱신할 수 있습니다.

### **Certbot의 주요 기능**

- Let's Encrypt 인증서를 발급받아 HTTPS를 활성화.
- 다양한 웹 서버(Nginx, Apache 등)와 연동하여 인증서를 자동으로 구성.
- 스케줄러(cron 또는 systemd)를 통해 인증서를 자동으로 갱신.
</aside>

<aside>
💡

Let`s Encrypt 인증서란

Let's Encrypt는 **비영리 인증 기관(CA, Certificate Authority)**으로, 누구나 무료로 SSL/TLS 인증서를 발급받을 수 있도록 지원함.

</aside>

### 6. 인증서 자동 갱신 설정

- **Crontab 열기**:
    
    ```bash
    sudo crontab -e
    ```
    
- **자동 갱신 명령 추가**:
    
    ```bash
    0 0 * * * certbot renew --quiet && systemctl restart nodejs
    ```
    
    - 매일 자정에 인증서를 갱신하고 Node.js 서버를 재시작합니다.
