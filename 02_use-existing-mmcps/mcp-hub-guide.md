# 1) MCP 서버 탐색 및 관리 허브

MCP 서버를 효과적으로 탐색하고 관리할 수 있는 플랫폼들이 다수 존재합니다.  
아래는 대표적인 MCP 허브 사이트들과 각각의 특징, 링크를 정리한 목록입니다.

---

## 🔍 1. [Smithery](https://smithery.ai)

- MCP 서버를 **검색, 테스트, 배포, 관리**할 수 있는 통합 플랫폼
- 공개 MCP 서버 탐색뿐 아니라 **자신만의 MCP 서버를 등록·호스팅**할 수도 있음
- 서버 설명, input/output 구조, ping 상태 등을 직관적으로 확인 가능
- Claude 및 Cursor와도 연동 편리

---

## 🛍️ 2. [Glama AI Marketplace](https://glama.ai)

- 다양한 MCP 서버를 **카테고리별로 정리된 마켓플레이스 형태**로 탐색 가능
- 사용자가 직접 만든 MCP 서버 공유 가능
- 언어별 필터링(Python, Rust 등) 및 로컬/원격 구분
- 메모리 MCP, Sequential Thinking MCP 등 고급 기능 포함

---

## ⭐ 3. [Awesome MCP Servers (GitHub)](https://github.com/ClementDelteil/awesome-mcp)

- 커뮤니티 기반 MCP 서버 모음 (Awesome 리스트 형식)
- GitHub 저장소, 사용 설명, 기능별 분류 포함
- 다양한 활용 예제와 코드 링크 확인 가능
- 오픈소스 관점에서 MCP를 탐색할 때 유용

---

## 📁 4. [MCP Directory](https://mcp.directory)

- 다양한 MCP 서버 정보를 **JSON 형식으로 정리·탐색**할 수 있는 웹 디렉토리
- 카테고리 및 기능 기반 탐색 가능
- `mcp.json` 직접 확인 및 복사 가능
- Claude, Cursor 등 호스트별 지원 정보 포함

---

## 🧭 5. [mcp.so](https://mcp.so)

- **Anthropic 및 Claude 생태계에서 MCP 서버를 가장 직관적으로 탐색**할 수 있는 메인 허브
- 실시간 서버 상태(ping), 응답 속도, 호출 예시 등 제공
- `tool_id`, `params`, `example_prompts` 등 모든 메타데이터 확인 가능
- **현재 등록된 MCP 수 천 개 이상** → 정렬/검색 기능 우수

---

## 📌 플랫폼별 비교 정리

| 플랫폼        | 탐색 기능 | 직접 배포 | JSON 보기 | 특징 요약 |
|---------------|-----------|------------|-----------|-----------|
| **Smithery**       | ✅ 매우 강력함 | ✅ 가능     | ✅ | 테스트 및 배포 중심 종합 도구 |
| **Glama AI**       | ✅ 다양함     | ✅ 가능     | ❌ | 마켓플레이스 중심 UI |
| **Awesome MCP**    | ✅ 문서 기반  | ❌ (PR 기반) | ✅ | 커뮤니티 중심 코드 탐색 |
| **MCP Directory**  | ✅ 빠름       | ❌          | ✅ | JSON 기반 탐색 도구 |
| **mcp.so**         | ✅ 실시간 상태 | ❌          | ✅ | 실전 MCP 실험 최적화 허브 |




---
# 2. 추천 클라이언트별 MCP 탐색 방식

MCP는 다양한 AI 앱·에디터·도우미 클라이언트에서 사용 가능하며,  
각 클라이언트는 고유한 방식으로 MCP 서버를 탐색하거나 실행할 수 있는 기능을 제공합니다.

---

### 🖥️ [Claude Desktop (Anthropic)](https://www.anthropic.com/index/claude-desktop)

- Anthropic 공식 데스크탑 앱
- MCP 서버를 설정에 직접 등록 가능
- 도구 사용 시 사용자 권한 요청 UI를 띄움
- Claude 기반 프로젝트 실습에 가장 적합

---

### 💻 [Cursor IDE](https://www.cursor.so)

- 코드 에디팅 특화 AI IDE (VS Code fork 기반)
- 내장된 MCP Composer를 통해 다양한 MCP 실행 가능
- GitHub 자동 커밋, README 생성 등 실전 사용 예 많음

---

### 🌊 [Windsurf](https://windsurf.ai)

- Claude 기반 AI 워크플로우 자동화 도구
- MCP 기반 파이프라인을 시각적으로 설계 가능
- Memory + Tool 조합 사용에 강점

---

### 🧠 [Continue (JetBrains/VS Code)](https://continue.dev)

- JetBrains 및 VS Code용 AI 확장 플러그인
- OpenCtx 기반 MCP 탐지 및 실행 지원
- Claude, GPT 등 다양한 모델과 연동 가능

---

### 🧩 [Sourcegraph Cody](https://sourcegraph.com/cody)

- GitHub 기반 AI 검색 및 문서화 도우미
- OpenCtx 기반 MCP 통합 및 실행
- 코드 분석 및 자동화 워크플로우에 적합

---

### 🖥️ [VS Code (Marketplace)](https://marketplace.visualstudio.com/items?itemName=SemanticWorkbenchTeam.mcp-server-vscode)

- 직접 확장(extension) 설치로 MCP 연동 가능
- `continue`, `cody` 플러그인에서 MCP 호출 지원

---

### 💬 [ChatWise](https://chatwise.app)

- 다양한 LLM 채팅 인터페이스 제공
- MCP 서버를 직접 추가하여 확장 가능

---

### 🍒 [Cherry Studio](https://github.com/CherryHQ/cherry-studio)

- Claude를 포함한 LLM 통합 클라이언트
- Windows, macOS, Linux에서 실행 가능

---

### 🧾 [Cline (CLI 클라이언트)](https://github.com/modelcontextprotocol/cline)

- Claude 기반 CLI MCP 클라이언트
- 터미널 환경에서 Claude + MCP 연동

---

### 🐣 [Witsy](https://github.com/witsy-ai/witsy)

- Claude 대화 흐름에 MCP 통합
- 메모리 + MCP 조합 기반 자동화 지원

---

### 🤝 [Enconvo](https://enconvo.com)

- Claude 중심의 팀 협업 및 DB 연동 플랫폼
- 다양한 MCP 서버 등록 및 워크스페이스 운영 지원

---

### 🌀 [Goose](https://gooseai.co)

- Claude + 도구 조합 기반 RAG/RPA 워크플로우 툴
- DB 분석, 자동 보고서 생성 등에 특화

---

### 🔄 [SpinAI](https://github.com/spinai/spinai)

- Claude용 MCP 연결 테스트 및 시뮬레이션 툴
- 입력/출력 구조 디버깅에 특화

---

## 🔖 참고

MCP를 직접 실험하거나 연결해보고 싶다면 **Claude Desktop**과 **Cursor**가 가장 안정적이며,  
워크플로우 자동화를 하고 싶다면 **Windsurf** 또는 **Goose**를 추천합니다.

각 클라이언트는 MCP 도구 사용 방식, 권한 처리, 호출 조건이 조금씩 다르므로  
테스트 시에는 가장 먼저 **권한 프롬프트 여부**와 **도구 응답 상태**를 확인해보세요.

---

## 🧭 어떤 클라이언트를 가장 많이 사용할까?

현재 기준으로 가장 널리 사용되는 클라이언트는 `Claude Desktop`과 `Cursor IDE` 입니다.

- **Claude Desktop**은 Anthropic의 공식 클라이언트로,  
  안정적인 프롬프트 흐름, 권한 요청 UI, 다양한 MCP와의 호환성이 가장 뛰어납니다.

- **Cursor IDE**는 개발자와 데이터 분석가들이 **코딩 중 직접 MCP를 호출**할 수 있어,  
  GitHub 연동, README 자동 생성, DB 조회 등 실제 업무에 활용도가 높습니다.

이외에도 Windsurf, Continue, Cline 등은 특정 목적(워크플로우, CLI, IDE 확장)에 따라 많이 쓰이며,  
Goose, Witsy 같은 도구는 **에이전트 기반 자동화**를 고려할 때 주목할 만한 클라이언트입니다.

> 🚀 처음 MCP를 사용해본다면 Claude Desktop이나 Cursor부터 시작하는 것을 추천합니다.



