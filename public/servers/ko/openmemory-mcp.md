---
name: OpenMemory MCP 서버
digest: OpenMemory는 Mem0으로 구동되는 로컬 메모리 인프라로, 어떤 AI 앱에서든 여러분의 메모리를 휴대할 수 있게 해줍니다. 애플리케이션 간에 중요한 것을 기억할 수 있도록 통합된 메모리 레이어를 제공합니다.
author: mem0ai
homepage: https://mem0.ai/openmemory-mcp
repository: https://github.com/mem0ai/mem0/tree/main/openmemory
capabilities:
  prompts: false
  resources: false
  tools: true
tags:
  - api
  - server
  - memory
icon: https://avatars.githubusercontent.com/u/137054526?s=48&v=4
createTime: 2025-05-14
featured: true
---

OpenMemory는 Mem0으로 구동되는 로컬 메모리 인프라로, 어떤 AI 앱에서든 여러분의 메모리를 휴대할 수 있게 해줍니다. 애플리케이션 간에 중요한 것을 기억할 수 있도록 통합된 메모리 레이어를 제공합니다.

![openmemory-demo](https://static.claudemcp.com/images/openmemory-mcp.png)

오늘, 우리는 첫 번째 구성 요소인 [OpenMemory MCP 서버](/ko/servers/openmemory-mcp)를 출시합니다. 이는 내장된 UI가 있는 프라이빗하고 로컬 우선의 메모리 레이어로, 모든 MCP 클라이언트와 호환됩니다.

## OpenMemory MCP 서버란?

**OpenMemory MCP 서버**는 MCP 호환 도구를 위한 공유되고 지속적인 메모리 레이어를 생성하는 프라이빗하고 로컬 우선의 메모리 서버입니다. 이는 완전히 여러분의 머신에서 실행되며, 도구 간에 원활한 컨텍스트 전달을 가능하게 합니다. 개발, 계획 또는 디버깅 환경 간에 전환하더라도 AI 어시스턴트는 반복적인 지시 없이 관련 메모리에 접근할 수 있습니다.

OpenMemory MCP 서버는 모든 메모리가 **로컬에, 구조화되어, 여러분의 통제 하에** 유지되도록 보장하며, 클라우드 동기화나 외부 저장소를 사용하지 않습니다.

## OpenMemory MCP 서버의 작동 방식

**모델 컨텍스트 프로토콜(Model Context Protocol, MCP)**을 기반으로 구축된 OpenMemory MCP 서버는 표준화된 메모리 도구 세트를 제공합니다:

- `add_memories`: 새로운 메모리 객체 저장
- `search_memory`: 관련 메모리 검색
- `list_memories`: 저장된 모든 메모리 보기
- `delete_all_memories`: 메모리 완전히 삭제

MCP 호환 도구는 서버에 연결하여 이러한 API를 사용해 메모리를 지속시키고 접근할 수 있습니다.

## 제공 기능

1.  **크로스 클라이언트 메모리 접근**: Cursor에서 컨텍스트를 저장하고 나중에 Claude나 Windsurf에서 반복 없이 검색할 수 있습니다.
2.  **완전한 로컬 메모리 저장소**: 모든 메모리는 여러분의 머신에 저장됩니다. 클라우드로 전송되지 않으며, 완전한 소유권과 통제권을 유지합니다.
3.  **통합 메모리 UI**: 내장된 OpenMemory 대시보드는 저장된 모든 것을 중앙에서 볼 수 있게 합니다. 대시보드에서 직접 메모리를 추가, 탐색, 삭제하고 클라이언트의 접근을 제어할 수 있습니다.

## 지원 클라이언트

OpenMemory MCP 서버는 모델 컨텍스트 프로토콜을 지원하는 모든 클라이언트와 호환됩니다. 이에는 다음이 포함됩니다:

- **Cursor**
- **Claude Desktop**
- **Windsurf**
- **Cline 등**

더 많은 AI 시스템이 MCP를 채택할수록, 여러분의 프라이빗 메모리는 더욱 가치 있게 됩니다.

---

## 설치 및 설정

OpenMemory를 시작하는 것은 간단하며 로컬 머신에서 몇 분 안에 설정할 수 있습니다. 다음 단계를 따르세요:

```bash
# 저장소 복제
git clone <https://github.com/mem0ai/mem0.git>
cd openmemory

# 백엔드 .env 파일 생성 (OpenAI 키 포함)
cd api
touch .env
echo "OPENAI_API_KEY=your_key_here" > .env

# 프로젝트 루트로 돌아가 Docker 이미지 빌드
cd ..
make build

# 모든 서비스 시작 (API 서버, 벡터 데이터베이스, MCP 서버 구성 요소)
make up

# 프론트엔드 시작
cp ui/.env.example ui/.env
make ui
```

**MCP 클라이언트 설정**  
Cursor, Claude Desktop 또는 기타 MCP 클라이언트를 연결하려면 사용자 ID가 필요합니다. 다음 명령어로 확인할 수 있습니다:

```bash
whoami
```

그런 다음 MCP 클라이언트에 다음 구성을 추가하세요 (`your-username`을 사용자 이름으로 대체):

```bash
npx install-mcp i "http://localhost:8765/mcp/<mcp-client>/sse/<your-username>" --client <mcp-client>
```

OpenMemory 대시보드는 `http://localhost:3000`에서 이용할 수 있습니다. 여기서 메모리를 보고 관리할 수 있으며, MCP 클라이언트와의 연결 상태를 확인할 수 있습니다.

설정이 완료되면 OpenMemory는 여러분의 머신에서 로컬로 실행되며, 모든 AI 메모리가 프라이빗하고 안전하게 유지되면서 호환되는 MCP 클라이언트 간에 접근 가능합니다.

### 실제 작동 확인 🎥

실제 작동 방식을 보여주는 짧은 데모를 준비했습니다:

<video
src="https://mem0.ai/blog/content/media/2025/05/Mem0-openMemory.mp4"
poster="https://img.spacergif.org/v1/3340x2160/0a/spacer.png"
width="3340"
height="2160"
controls
playsinline
preload="metadata"
style="background: transparent url('https://mem0.ai/blog/content/media/2025/05/Mem0-openMemory_thumb.jpg') 50% 50% / cover no-repeat;"></video>

## 실제 사례

**시나리오 1: 크로스 도구 프로젝트 흐름** Claude Desktop에서 프로젝트의 기술 요구사항을 정의합니다. Cursor에서 빌드합니다. Windsurf에서 문제를 디버깅합니다 - 모두 OpenMemory를 통해 공유 컨텍스트가 전달됩니다.

**시나리오 2: 지속되는 환경 설정** 한 도구에서 선호하는 코드 스타일이나 톤을 설정합니다. 다른 MCP 클라이언트로 전환하면 동일한 환경 설정을 재정의 없이 접근할 수 있습니다.

**시나리오 3: 프로젝트 지식**

중요한 프로젝트 세부 사항을 한 번 저장하면, 호환되는 모든 AI 도구에서 접근할 수 있어 반복적인 설명이 필요 없습니다.

## 결론

OpenMemory MCP 서버는 **MCP 호환 도구에 메모리를 제공**하면서 통제권이나 프라이버시를 포기하지 않습니다. 이는 현대 LLM 워크플로우의 근본적인 한계인 도구, 세션, 환경 간의 컨텍스트 손실을 해결합니다.

메모리 작업을 표준화하고 모든 데이터를 로컬에 유지함으로써, 토큰 오버헤드를 줄이고 성능을 개선하며, 성장하는 AI 어시스턴트 생태계 전반에 걸쳐 더 지능한 상호작용을 가능하게 합니다.

이것은 시작에 불과합니다. MCP 서버는 OpenMemory 플랫폼의 첫 번째 핵심 레이어로, AI 시스템 전반에 걸쳐 메모리를 휴대 가능하고 프라이빗하며 상호 운용 가능하게 만드는 더 넓은 노력의 일환입니다.

OpenMemory MCP를 통해 여러분의 AI 메모리는 프라이빗하고 휴대 가능하며 여러분의 통제 하에, 정확히 있어야 할 곳에 머무릅니다.
