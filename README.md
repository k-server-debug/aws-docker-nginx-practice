# AWS EC2 + Docker + Nginx Practice

## 목표

AWS EC2 서버에 Docker를 설치하고 nginx 컨테이너를 실행하여 직접 만든 HTML 페이지를 배포한다.

---

## 사용 기술

* AWS EC2
* Amazon Linux
* SSH
* Nginx
* Docker
* Docker Compose

---

## 구성도

Browser
↓
Security Group (8000)
↓
EC2
↓
Docker Container (nginx)
↓
index.html

---

## 실습 내용

### 1. EC2 생성 및 SSH 접속

* AWS EC2 인스턴스 생성
* Security Group 설정
* SSH 접속 성공

### 2. nginx 설치

* nginx 설치
* 서비스 실행
* 브라우저 접속 확인

### 3. Docker 설치

* Docker 설치
* Docker 서비스 실행
* nginx 컨테이너 실행

### 4. Volume 연결

/myweb 폴더 생성

index.html 작성

Docker Volume을 이용하여 nginx와 연결

### 5. Docker Compose

docker-compose.yml 생성

Compose를 이용한 nginx 컨테이너 실행

### 6. Dockerfile

Dockerfile 작성

FROM nginx

COPY index.html /usr/share/nginx/html/index.html

Docker Image 생성

docker build -t myweb .

직접 만든 이미지 실행

docker run -d -p 8000:80 myweb

---

## 문제 해결 경험

### 문제 1

SSH 접속 실패

원인:

* pem 파일 이름 오타

해결:

* 올바른 pem 파일 지정

---

### 문제 2

브라우저 접속 실패

원인:

* Security Group 8000 포트 미개방

해결:

* Inbound Rule 추가

---

### 문제 3

port is already allocated

원인:

* 기존 컨테이너가 8000 포트 사용 중

해결:

* 기존 컨테이너 stop / rm

---

### 문제 4

permission denied

원인:

* Dockerfile 생성 권한 부족

해결:

* sudo 사용

---

## 배운 점

* Docker Image와 Container의 차이
* Port Mapping 개념
* Volume 연결 방법
* Docker Compose 사용법
* Dockerfile을 통한 이미지 생성
* AWS Security Group 역할
* 문제 발생 시 원인 추적 방법
