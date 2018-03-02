보안을 위해 HTTPS 인증 설정을 해야한다. 하지 않으면 신뢰하지 않는 사이트라고 브라우저에서 나타나기 때문에 설정이 필요한데, 이를 피하기 위해 무료 HTTPS 인증서인 letsencrypt를 설정해보자.

## Certbot

letsencrypt의 복잡한 설정방법을 손쉽게 해주는 도구이다. 자동으로 설치뿐만 아니라, 갱신도 가능하다. nginx와 CenOS 6에 설치한다.

## HTTPS 설치과정

https://certbot.eff.org/#centos6-nginx

certbot 다운

```
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
```

인증서 설치

```
sudo ./certbot-auto certonly --agree-tos --no-eff-email --email user@email.com --webroot -w /path/to/nginx/html -d your.domain.com
```

바로 설치되긴 만무하다... python기반이라 필요한 패키지 설치가 필요할 수도 있다. 아 그리고 해당 nginx가 떠있어야 정상적으로 설치된다.

설치가 완료되면,

```
sudo ls /etc/letsencrypt/live/
your.domain.com

sudo ls /etc/letsencrypt/live/your.domain.com
README  cert.pem  chain.pem  fullchain.pem  privkey.pem
```

해당 경로에 도메인으로 디렉토리가 생기고 그 안에 pem파일들을 확인할 수 있다.

## nginx 설정

실 서비스하는 서버는 다른 서버이며, nginx는 두 개의 다른 도메인을 한번에 https인증하기 위한 proxy 서버로 두려고 한다.
nginx를 https로 사용하려면 빌드시 추가로 설정해줘야하는 부분이 있는데, 이는 nginx 설치 포스팅에서 확인하기 바란다.
443포트로 들어오는 요청은 https 인증 후 8001 포트로 리다이렉트를 한다.
또한 기존 80포트는 301 request code를 반환하여 https로 리다이렉트하도록 유도한다.

정리하자면,

* 80포트는 사용하지 않음. (443포트로 리다이렉트)
* 해당 장비에는 your.domain.com, my.domain.com 2개의 도메인이 있고, 둘 다 https 인증 필요
* nginx는 https 인증을 위한 단순 proxy 서버 (8001로 proxy)


```sh
# nginx.conf

# 80 port redirect
server {
  listen 80;
  return 301 https://$host$request_uri;
}

# my.domain.com https
server {
  listen 443 ssl;
  server_name my.domain.com;

  ssl_certificate       /etc/letsencrypt/live/my.domain.com/fullchain.pem;
  ssl_certificate_key   /etc/letsencrypt/live/my.domain.com/privkey.pem;
  ssl_session_cache     shared:SSL:1m;
  ssl_session_timeout   5m;

  location / {
    proxy_pass http://127.0.0.1:8001/;
    proxy_redirect off;

    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header Host "nginx.for.https";
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
  }  
}

# your.domain.com https
server {
  listen 443 ssl;
  server_name your.domain.com;

  ssl_certificate       /etc/letsencrypt/live/your.domain.com/fullchain.pem;
  ssl_certificate_key   /etc/letsencrypt/live/your.domain.com/privkey.pem;
  ssl_session_cache     shared:SSL:1m;
  ssl_session_timeout   5m;

  location / {
    proxy_pass http://127.0.0.1:8001/;
    proxy_redirect off;

    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header Host "nginx.for.https";
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
  }  
}
```

## 정리

해당 포스트에는 자동 갱신에 대한 내용은 빠져있다. 이 부분은 certbot 사이트에서 확인하길 바란다.

