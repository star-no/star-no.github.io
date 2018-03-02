---
layout: post
title: nginx 설치
tags: [nginx, server]
---
nginx의 장점은 인터넷에 너무나 많고 좋은 글들로 설명이 되어있다.
설치도 쉽고 사용도 쉽다. 가벼운 반면에 성능이 나쁘지 않아서 많이들 사용한다.
나는 보통 간단한 proxy 서버나 load balancing 용도로 사용해왔었다.

## 설치하기

http://nginx.org/en/download.html
nginx 공식 홈페이지에서 해당 os에 맞는 파일을 다운로드하자.
나는 포스팅 당시 stable 버전인 nginx-1.12.2 를 선택

소스코드를 다운받아 직접 compile하고 build할 예정이다.
(입맛대로 옵션을 설치할 수 있는 장점이 있다.)

```
wget http://nginx.org/download/nginx-1.12.2.tar.gz
tar -xvf nginx-1.12.2.tar.gz
```

보통의 오픈소스 프로그램은 configuration파일이 존재하고 이를 통해 help나 다른 설치 옵션을 지정하여 설정할 수 있다.

nginx 역시 `configuration --help` 명령하면 많은 빌드옵션을 확인할 수 있다.
이 중 중요한 옵션은 `--prefix`이며 이 옵션을 통해 설치 경로를 지정할 수 있다.
추가로 https를 지원하려면 `--with-http_ssl_module` 옵션이 필요하고
TCP/UDP proxy를 위한 `--with-stream` 옵션이나
ngix 멀티 쓰레딩을 위한 `--with-threads` 옵션도 있으니 잘 참고할 것.

compile 설정을 하고 build하면 완료.

```
./configuration --help
./configuration --prefix=~/path/to/nginx/to/install
make -j
make install
```

## nginx 간단한 사용

해당경로로 가면 빌드되어 설치된 것을 확인할 수 있다.

`sbin` 디렉토리에 nginx 바이너리 파일이 실행파일이며 이 파일을 path경로로 잡아주면 어디서든 사용가능하다.

* 실행 : ./nginx
* 종료 : ./nginx -s stop
* 재실행 : ./nginx -s reload

`conf/nginx.conf`는 nginx에 대한 설정파일이다.

## 후기

nginx는 앞서 말했듯이 정말 간단하게 설치하고 사용하기 쉽다.
쉬운 반면에 막강한 기능들을 제공하고 있으니, 익혀두고 사용한다면 언젠간 큰 도움이 된다.
`nginx.conf`에 설정에 관한 정보는 공식홈페이지를 통해 확인하고 사용하도록 하자.

