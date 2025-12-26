---
title: ChatGPT Apps SDK 예제 실행해보기
#date: 2013-12-24 23:30:09
categories:
  - Project

tags:
  - ChatGPT
  - Apps-SDK

photos:
  - ![mcp architecture](/post_images/mcp_architecture.png)
---

ChatGPT에 앱을 등록해보기 위해, <br>
공식문서에서 제공하는 예제 코드를 먼저 실행해보았습니다. <br>

이 글에서는 예제 코드를 실행하면서 GPT에 앱을 등록하고 실제로 동작시키기까지의 과정을 정리합니다.

## openai-apps-sdk-examples

> [openai-apps-sdk-examples](https://github.com/openai/openai-apps-sdk-examples?tab=readme-ov-file) <br>
> 전체적인 진행 과정은 공식문서에 제공된 README를 참고하여 진행했습니다.

## 프로그램 세팅 과정

아래는 openai-apps-sdk-examples 예제를 로컬에서 실행하기 위한 전체 흐름입니다.
<br>

1. 코드 clone 후 의존성 설치(pnpm install)를 진행합니다.
2. UI를 빌드합니다. (pnpm run build)
3. assets 폴더를 정적 서버로 띄웁니다. (pnpm run serve)
4. 예제 중 pizzaz_server_node 서버로 진행하기 위해 해당 폴더로 이동하여 서버를 실행합니다.
   1. cd pizzaz_server_node
   2. pnpm start

### 전체 구조

```
[ build ]
src/  ──▶ assets/        (UI 정적 파일)

[ serve ]
assets/ ──▶ :4444        (UI 서버)

[ start ]
pizzaz_server_node ──▶ :8000 (MCP 서버)

```

## ngrok 설정 과정

ngrok은 로컬 서버에서 실행중인 앱을 외부에서 접근 가능한 공개 HTTPS 주소로 만들어주기 위해 사용하는 도구입니다.

```
내 PC에서 실행 중인 app (localhost:8000)
        ↓
ngrok
        ↓
공개 HTTPS 주소 (https://xxxx.ngrok-free.dev)

```

=> GPT와 로컬서버를 이어주는 중간 다리 역할을 해줍니다.

### ngrok 실행 순서

1. ngrok 설치
   1. ngrok.exe 위치로 이동
   2. ".\ngrok.exe version" 명령어를 통해 실행여부를 확인합니다.
2. ngrok 대시보드에 로그인 한 후 토큰을 등록합니다.
   1. [ngrok대시보드 사이트](https://dashboard.ngrok.com/get-started/your-authtoken)
   2. ".\ngrok.exe config add-authtoken YOUR_AUTH_TOKEN " 명령어를 통해 토큰을 등록합니다.
   3. "Authtoken saved to configuration file"이 출력되면 정상 등록이 되었습니다.
   4. ".\ngrok.exe config check" 명령어를 통해 설정을 확인할 수 있습니다.
3. ngrok으로 8000 포트 포워딩을 진행합니다.
   1. ".\ngrok.exe http 8000" 명령어를 통해 포워딩을 진행합니다.
      ![ngrok terminal 화면](/post_images/portforwading.png)
   2. Forwarding 결과 생성된 URL을 복사합니다.

## GPT Apps 등록

1. GPT 홈페이지에 접속한 뒤 -> 앱 -> 설정 -> 고급 설정 -> 앱만들기 를 클릭합니다.
   ![gpt app등록화면](/post_images/gpt.png)
2. ngrok에서 생성한 URL을 "MCP 서버 URL"에 작성합니다.
3. 인증은 "인증없음"을 선택합니다.

## 실행 (주의할 점)

![error](/post_images/ui_error.png)

실행해 보니 서버 자체는 정상적으로 동작했지만, ChatGPT 화면에서는 UI가 출력되지 않는 문제가 발생했습니다.
원인을 확인해 보니, Chrome을 사용 중이고 최근 버전(142 이상) 으로 업데이트된 경우에는 보안 정책 변경으로 인해 로컬 네트워크 접근이 기본적으로 차단되어 있었습니다.
<br>

이로 인해 UI가 여전히 localhost 기반의 로컬 서버를 바라보고 있었고, 해당 요청이 차단되면서 UI가 정상적으로 렌더링되지 않았던 것입니다.

**비활성화 방법**

1. [ChromeFlagUrl](chrome://flags/) 접속
2. "Local Network Access Checks" Disabled로 변경
   ![flag화면](/post_images/flags.png)

## 결과

![app실행결과](/post_images/success.png)

Chrome의 "local-network-access" 관련 플래그를 disabled 처리하니, 로컬 서버 접근이 가능했고, UI가 정상적으로 출력됐습니다.

OpenAI의 Apps SDK는 MCP 구조를 기반으로 앱을 구성하기 때문에, 일반적인 웹 애플리케이션 개발과는 다른 관점에서 접근할 필요가 있었습니다. <br>
단순히 서버를 실행하고 화면을 띄우는 것이 아니라, ChatGPT가 어떤 방식으로 서버와 통신하고, UI를 구성하는지 <br> **실행환경에 대한 이해가 중요하다는 점** 을 느꼈습니다.

<br>

이번 예제를 통해 MCP 서버의 개념과 실행흐름을 정리해보았습니다.

![mcp architecture](/post_images/mcp_architecture.png)
