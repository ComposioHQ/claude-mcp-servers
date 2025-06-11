---
name: Apify 테스터 MCP 클라이언트
digest: AI 에이전트를 Apify의 5,000개 이상의 웹 스크래핑 및 자동화 Actor 생태계에 연결하여 웹사이트, 소셜 미디어, 검색 엔진, 지도에서 데이터를 추출할 수 있도록 하는 클라이언트입니다.
author: Apify
homepage: https://apify.com/jiri.spilka/tester-mcp-client
docs: https://mcp.apify.com
icon: https://apify.com/ext/apify-symbol-512px.svg
windows: true
mac: true
linux: true
featured: true
tags:
  - 웹 스크래핑
  - Apify Actor
  - 통합
createTime: 2025-05-13
---

## 🚀 주요 기능

- 🔌 Server-Sent Events(SSE)를 사용하여 MCP 서버에 연결
- 💬 도구 호출 및 결과를 표시하는 채팅형 UI 제공
- 🇦 하나 이상의 Apify Actor와 상호작용하기 위한 [Apify MCP 서버](https://apify.com/apify/actors-mcp-server) 연결 지원
- 💥 컨텍스트와 사용자 질의에 따라 동적으로 도구 사용 (서버 지원 시)
- 🔓 인증 헤더와 API 키를 사용한 보안 연결
- 🪟 오픈 소스로 코드 검토, 개선 제안 또는 수정 가능

## 🎯 테스터 MCP 클라이언트의 역할

[Actors-MCP-Server](https://apify.com/apify/actors-mcp-server)에 연결 시 대화형 채팅 인터페이스를 통해 다음을 수행할 수 있습니다:

- "소셜 미디어 스크래핑에 가장 인기 있는 Actor는 무엇인가요?"
- "Instagram 스크래퍼 사용법을 알려주세요"
- "LinkedIn에서 데이터를 추출하려면 어떤 Actor를 사용해야 하나요?"
- "Google 검색 결과 스크래핑 방법을 도와주실 수 있나요?"

![테스터-MCP-클라이언트-스크린샷](https://raw.githubusercontent.com/apify/tester-mcp-client/refs/heads/main/docs/chat-ui.png)

## 📖 작동 방식

Apify MCP 클라이언트는 Server-Sent Events(SSE)를 통해 실행 중인 MCP 서버에 연결하여 다음을 수행합니다:

- MCP 서버의 `/sse`로 SSE 연결 시작
- `POST /message`를 통해 사용자 질의 전송
- LLM 출력 및 **도구 사용** 블록을 포함할 수 있는 실시간 스트리밍 응답 수신(`GET /sse` 통해)
- LLM 응답에 따라 도구 호출 조정 및 대화 표시
- 대화 내용 표시

## ⚙️ 사용 방법

- SSE를 지원하는 모든 MCP 서버 테스트 가능
- [Apify Actors MCP 서버](https://apify.com/apify/actors-mcp-server) 테스트 및 3000개 이상의 도구 중 동적 선택 기능 확인

### 일반 모드(Apify에서)

Apify에서 테스터 MCP 클라이언트를 실행하고 SSE를 지원하는 모든 MCP 서버에 연결할 수 있습니다.
MCP 서버 URL, 시스템 프롬프트, API 키 등의 매개변수를 지정하여 Apify UI 또는 API를 통해 구성할 수 있습니다.

Actor 실행 후 로그에서 테스터 MCP 클라이언트 UI 링크 확인:

```shell
INFO  MCP 서버와 상호작용하려면 브라우저에서 https://......runs.apify.net로 이동하세요.
```

### 대기 모드(Apify에서)

개발 중 🚧

## 💰 가격 정책

Apify MCP 클라이언트는 무료로 사용할 수 있습니다. LLM 제공자 사용량 및 Apify 플랫폼에서 소비된 리소스에 대해서만 비용이 발생합니다.

이 Actor는 [이벤트별 과금](https://docs.apify.com/sdk/js/docs/guides/pay-per-event)이라는 현대적이고 유연한 AI 에이전트 수익화 및 가격 정책을 사용합니다.

과금 이벤트:

- Actor 시작(사용 메모리 기준, 128MB 단위당 과금)
- 실행 시간(5분마다 과금, 128MB 단위당)
- 질의 응답(사용 모델에 따라 다름, 자체 LLM 제공자 API 키 사용 시 과금 없음)

자체 LLM 제공자 API 키 사용 시 128MB 메모리로 1시간 실행 비용은 약 $0.06입니다.
Apify 무료 티어(신용카드 불필요 💳)로 월 80시간까지 실행 가능합니다.
MCP 서버 테스트에 충분한 시간입니다!

## 📖 작동 원리

```plaintext
브라우저 ← (SSE) → 테스터 MCP 클라이언트 ← (SSE) → MCP 서버
```

이 체인을 구성하여 사용자 정의 브리징 로직을 테스터 MCP 클라이언트 내부에 유지하면서 주요 MCP 서버를 변경하지 않습니다.
브라우저는 SSE를 사용하여 테스터 MCP 클라이언트와 통신하고, 테스터 MCP 클라이언트는 SSE를 통해 MCP 서버와 통신합니다.
이를 통해 추가 클라이언트 측 로직을 코어 서버와 분리하여 유지보수 및 디버깅이 용이해집니다.

1. `https://tester-mcp-client.apify.actor?token=YOUR-API-TOKEN`으로 이동(로컬 실행 시 http://localhost:3000)
2. `public/` 디렉터리에서 `index.html` 및 `client.js` 파일 제공
3. 브라우저가 `GET /sse`를 통해 SSE 스트림 열기
4. 사용자 질의를 `POST /message`로 전송
5. 질의 처리:
   - 대형 언어 모델 호출
   - 필요한 경우 도구 호출
6. 각 결과 청크에 대해 `sseEmit(role, content)` 실행

### 로컬 개발

테스터 MCP 클라이언트 Actor는 [GitHub](https://github.com/apify/rag-web-browser)에서 오픈 소스로 제공되며 필요에 따라 수정 및 개발이 가능합니다.

소스 코드 다운로드:

```bash
git clone https://github.com/apify/tester-mcp-client.git
cd tester-mcp-client
```

의존성 설치:

```shell
npm install
```

`.env.example` 파일을 참조하여 `.env` 파일 생성:

```plaintext
APIFY_TOKEN=YOUR_APIFY_TOKEN
LLM_PROVIDER_API_KEY=YOUR_API_KEY
```

`const.ts` 파일에서 `mcpUrl`, `systemPrompt` 등의 기본 설정 값 정의. 개발 필요에 따라 조정 가능.

로컬에서 클라이언트 실행

```bash
npm start
```

브라우저에서 [http://localhost:3000](http://localhost:3000)으로 이동하여 MCP 서버와 상호작용.

**Apify Actor와 즐거운 대화 되세요!**

## ⓘ 제한 사항 및 피드백

클라이언트는 Prompts 및 Resource와 같은 모든 MCP 기능을 지원하지 않습니다.
또한 대화 내용을 저장하지 않아 페이지 새로고침 시 채팅 기록이 초기화됩니다.

## 참고 자료

- [모델 컨텍스트 프로토콜](https://modelcontextprotocol.org/)
- [Apify Actors MCP 서버](https://apify.com/apify/actors-mcp-server)
- [이벤트별 과금 모델](https://docs.apify.com/sdk/js/docs/guides/pay-per-event)
- [AI 에이전트란?](https://blog.apify.com/what-are-ai-agents/)
- [MCP란 무엇이며 왜 중요한가?](https://blog.apify.com/what-is-model-context-protocol/)
