---
title: ChatGPT 앱 기반 MCP 서버 구축과 Docker 배포
categories:
  - Project

tags:
  - ChatGPT
  - Docker
  - Ncloud

photos:
  - /post_images/cicd.png
---

스위프 웹 프로젝트에 참여하면서 단순한 웹 서비스 개발을 넘어,
ChatGPT 앱 기반으로도 사용할 수 있는 구조를 설계하고 구현하는 경험을 하게 되었습니다.

이번 프로젝트에서는 웹 서비스(Next.js)와 API 서버(Java) 뿐만 아니라,
ChatGPT 앱과 연동하여 앱 형태로도 활용할 수 있는 MCP 서버 (NestJS)까지 함께 구축했습니다.

또한 각 서비스를 Docker로 컨테이너화 하고, PostgresSQL과 Nginx를 포함한 전체 인프라를 docker-compose로 통합 구성한 뒤, Ncloud 환경에 배포하는 과정을 담당했습니다.

이 글에서는 해당 프로젝트의 전체 아키텍처 구성부터 Docker 기반 서비스 분리, Proxy 설정, 그리고 클라우드 배포 과정까지 정리해보려고 합니다.

## 1. 프로젝트 구성

이번 프로젝트는 다음과 같은 구조로 진행했습니다.

| 구성 요소          | 포트 | 설명             |
| ------------------ | ---- | ---------------- |
| Backend (Java)     | 8080 | REST API 서버    |
| Frontend (Next.js) | 3000 | 사용자 웹 서비스 |
| MCP (NestJS)       | 3001 | 별도 서비스 서버 |
| PostgreSQL         | 5432 | 데이터베이스     |
| Nginx              | 80   | Reverse Proxy    |

---

## 2. Docker로 배포한 이유

여러 구성 요소를 하나의 서버에서 함께 운영해야 했습니다. <br>
이 과정에서 실행 환경을 통일하고, 배포를 단순화하기 위해 Docker를 선택했습니다.

### Docker의 장점

1. 개발 환경과 운영 환경을 동일하게 운영 할 수 있습니다.
   - 로컬에서 정상 동작하던 서비스가 서버에서 오류가 나는 이유는 단순한 버전 차이뿐 아니라, 운영체제, 환경변수, 네트워크 구조 등 실행 환경의 차이 때문입니다. Docker는 이러한 실행 환경 자체를 이미지로 고정하여 환경 불일치 문제를 최소화해줍니다.

2. 여러 서비스를 구조적으로 관리할 수 있습니다.
   - Docker가 없었다면 위의 내용을 구성하기 위해 Node, Java, PostgreSQL, Nginx, 환경변수설정, 포트 충돌 관리를 서버에 직접 세팅해야 했습니다.
   - Docker를 사용하면 각 서비스를 컨테이너 단위로 격리할 수 있고, 서버에는 Docker만 설치하면 됩니다.

3. 배포 과정을 단순화 할 수 있습니다.

- Docker를 사용하지 않았다면 의존성 설치 -> 빌드 -> 프로세스 실행 -> 서비스별로 반복
- Docker를 사용하면, "docker-compose up -d"

### docker-compose.yml

컨테이너를 각각 띄우면 옵션이 많아지고 실수하기 쉽습니다. <br>
서비스 간 네트워크 연결, 의존성 순서, 환경변수, 볼륨을 한 파일에서 관리 할 수 있습니다.

```
version: "3.8"

services:
  frontend:
    build: ./frontend
    container_name: frontend
    expose:
      - "3000"

  backend:
    build: ./backend
    container_name: backend
    expose:
      - "8080"
    environment:
      DB_HOST: postgres

  mcp:
    build: ./mcp
    container_name: mcp
    expose:
      - "3001"

  postgres:
    image: postgres:15
    container_name: postgres
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - postgres-data:/var/lib/postgresql/data

  nginx:
    image: nginx:latest
    container_name: nginx
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - frontend
      - backend
      - mcp

volumes:
  postgres-data:

```

### 자동 배포를 위한 Docker hub 사용

- CI/CD로 전환하기 위해 Docker hub를 사용하였습니다.

#### 추후 작성할 내용

- Nginx Proxy 문제
- Next.js 빌드 시 환경변수 적용 문제
- Ncloud 적용기
