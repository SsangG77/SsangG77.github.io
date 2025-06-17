---
title: 
date: 2025-03-02 12:00:00 +0900
categories: [가이드]
tags: [GCP, NodeJS, 서버, express]
---

해당 예제는 `brawlytics-server.com`에 해당함.

### 1. 기존 포트 80 점유 프로세스 종료

포트 80을 점유한 프로세스 확인

```bash
sudo netstat -tulnp | grep :80
```

포트 80을 점유한 프로세스 강제 종료

```bash
sudo fuser -k 80/tcp
```

### 2. 인증서 갱신 (또는 확인)

기존 인증서 삭제 (필요한 경우)

```bash
sudo certbot delete --cert-name brawlytics-server.com
```

새로운 인증서 발급

```bash
sudo certbot certonly --standalone -d brawlytics-server.com
```

인증서 상태 확인

```bash
sudo certbot renew --dry-run
```

결과: 인증서가 정상적으로 갱신됨.

```
Certificate is saved at: /etc/letsencrypt/live/brawlytics-server.com/fullchain.pem
Key is saved at: /etc/letsencrypt/live/brawlytics-server.com/privkey.pem
This certificate expires on 2025-06-06.

```

### 3. Node.js 서버 다시 실행

PM2로 서버 실행

```bash
pm2 start index.js --name brawlytics-server
```

PM2 설정 저장 (서버 재부팅 시 자동 실행)

```bash
pm2 save
pm2 startup
```

서버가 정상적으로 실행되었는지 확인

```bash
pm2 list
```

### 4. HTTPS 정상 동작 확인

HTTPS 요청 테스트

```bash
curl -I https://brawlytics-server.com
```

결과 (성공)

```
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Date: Sat, 08 Mar 2025 05:25:28 GMT
Connection: keep-alive
```

이 응답이 나오면 HTTPS가 정상 작동하는 것.
