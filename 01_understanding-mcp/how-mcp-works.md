# MCP는 어떻게 작동하는가?

MCP(Model Context Protocol)는 모델이 외부 도구를 사용할 수 있도록 하는 통신 구조이며,  
기본적으로는 **클라이언트-서버 모델**을 따른다.  
이 문서에서는 MCP의 작동 구조를 실제 흐름 중심으로 설명하며,  
Claude Desktop 환경에서의 실제 예시를 통해 상세히 이해할 수 있도록 정리한다.

---

## 1. 프로토콜 초기 연결: MCP 핸드셰이크

MCP 클라이언트(예: Claude Desktop)가 시작되면, 가장 먼저 MCP 서버들과의 **초기 핸드셰이크(handshake)** 과정을 수행한다.

### 🔁 Handshake 과정

1. **초기 연결**  
   클라이언트가 설정된 MCP 서버들과 연결을 시작한다.

2. **Capability Discovery**  
   각 MCP 서버에 “당신이 제공하는 도구는 무엇인가요?”라고 요청한다.  
   서버는 자신이 제공 가능한 기능(tool list, description, prompts 등)을 응답한다.

3. **등록 및 활용 가능 상태 전환**  
   클라이언트는 이 기능 목록을 **AI 모델이 사용할 수 있도록 등록**한다.  
   이후 대화 중 사용자의 요청이 해당 기능과 매칭될 경우, AI는 이 도구를 호출할 수 있게 된다.

## 1-1. MCP 아키텍처와 구성요소

MCP는 LLM 기반 애플리케이션이 외부 도구와 안전하게 통신할 수 있도록 설계된 **클라이언트-서버 프로토콜**입니다.  
아래는 MCP 아키텍처를 구성하는 주요 요소들입니다:

### 🔧 MCP의 주요 구성요소

| 요소 | 설명 |
|------|------|
| **Host** | Claude, Cursor 등 모델이 구동되는 애플리케이션. MCP 클라이언트를 내장하고 있음 |
| **Client** | MCP 프로토콜 메시지를 처리하고 서버와 통신을 관리하는 중간 계층 |
| **Server** | 실제 도구(API, DB 등)를 구현하고 클라이언트에 기능(capabilities)을 제공하는 실행 주체 |

<img width="854" alt="image" src="https://github.com/user-attachments/assets/38843414-1ae9-40a4-9ea1-2b18caabc6ec" />


MCP는 **LSP(Language Server Protocol)**에서 영감을 받은 구조로,  
여러 종류의 도구(MCP 서버)를 동적으로 클라이언트에 연결하고 사용할 수 있게 합니다.

---

### 🧱 프로토콜 계층 구조

MCP는 두 가지 계층으로 나뉩니다:

1. **Protocol Layer 프로토콜 계층**  
- MCP의 핵심 로직 처리 계층입니다.
- 메시지 구성, 요청/응답 연결, 알림 처리 등 고수준 통신 패턴을 담당합니다.
- 내부적으로는 `Protocol`, `Client`, `Server` 등의 클래스로 구성됩니다.


#### 📄 Python 코드 예시 (Session 클래스)

```python
class Session(BaseSession[RequestT, NotificationT, ResultT]):
    async def send_request(
        self,
        request: RequestT,
        result_type: type[Result]
    ) -> Result:
        """
        요청을 전송하고 응답을 기다립니다.
        오류 응답 시 McpError 예외 발생.
        """
        # 요청 처리 로직 구현

    async def send_notification(
        self,
        notification: NotificationT
    ) -> None:
        """
        응답을 기대하지 않는 일방적인 알림을 보냅니다.
        """
        # 알림 처리 로직 구현

    async def _received_request(
        self,
        responder: RequestResponder[ReceiveRequestT, ResultT]
    ) -> None:
        """
        상대방으로부터 들어온 요청을 처리합니다.
        """
        # 요청 수신 처리

    async def _received_notification(
        self,
        notification: ReceiveNotificationT
    ) -> None:
        """
        상대방으로부터 들어온 알림을 처리합니다.
        """
        # 알림 수신 처리




2. **Transport Layer (전송 계층)**


맞아요 미소님, 지적 정확하십니다!  
`5-2. Transport Layer`부터 마크다운 양식이 일부 깨져서 코드 블록/표 스타일이 Markdown에서 제대로 렌더링되지 않을 수 있어요.  
그래서 아래는 **`how-mcp-works.md`의 5-2 섹션부터 완전히 정리한 버전**입니다.

---

### ✅ `how-mcp-works.md` – 5-2부터 마크다운 양식 보완본

```markdown
---

### 2. Transport Layer (전송 계층)

MCP의 Transport Layer는 **실제 메시지를 주고받는 통신 계층**입니다.  
클라이언트와 서버 사이의 물리적 연결을 관리하며, 다양한 방식으로 구성할 수 있습니다.

#### ✅ 주요 전송 방식

| 전송 방식         | 설명 |
|------------------|------|
| **Stdio**        | 표준 입력/출력 기반 전송<br>로컬 환경에서 테스트에 적합 |
| **HTTP + SSE**   | 클라이언트는 HTTP POST 방식으로 요청을 전송하고,<br>서버는 SSE(Server-Sent Events)를 통해 응답을 push |

> MCP는 모든 전송 방식에서 **JSON-RPC 2.0** 포맷을 사용하여 메시지를 교환합니다.

---

#### 📬 메시지 포맷 예시 (JSON-RPC 기반)

```json
// 📤 요청 메시지 (Request)
{
  "jsonrpc": "2.0",
  "method": "getWeather",
  "params": {
    "city": "Seoul"
  },
  "id": 1
}
```

```json
// ✅ 응답 메시지 (Result)
{
  "jsonrpc": "2.0",
  "result": {
    "temp": "13°C",
    "condition": "맑음"
  },
  "id": 1
}
```

```json
// ❌ 오류 메시지 (Error)
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32601,
    "message": "Method not found"
  },
  "id": 1
}
```

> `Request`, `Result`, `Error`, `Notification` 총 4가지 유형의 메시지를 기본으로 사용합니다.
```

---



---

### 🔁 연결 수명 주기 요약

1. Initialization
plaintext

[Client] → initialize 요청
[Server] ← initialize 응답
[Client] → initialized 알림
초기 기능 탐색 및 연결 완료

2. Message Exchange
Request ↔ Response

Notification (일방향 메시지)

json
复制
编辑
{ "jsonrpc": "2.0", "method": "progress", "params": { "percentage": 70 } }
3. Termination
close() 호출

오류 또는 연결 종료

7. 오류 처리 (Error Handling)
오류 코드	설명
-32700	JSON 파싱 오류
-32600	잘못된 요청 형식
-32601	메서드 없음
-32602	파라미터 오류
-32603	내부 처리 오류
-32000 이상은 커스텀 오류 코드 가능

오류는 응답/전송/핸들러를 통해 전파됨



---

## 2. 사용자 요청 시 내부 흐름 (Step by Step)

사용자가 Claude Desktop에게 “샌프란시스코 오늘 날씨 알려줘”라고 요청했을 때, 내부에서는 다음과 같은 과정이 일어난다.

### 📘 전체 흐름

1. **사용자 입력 감지**  
   사용자가 자연어로 질문을 입력한다.

2. **필요성 인식 (Need Recognition)**  
   Claude는 해당 질문에 **실시간 외부 정보가 필요함**을 인식한다.  
   이 정보는 사전 학습된 지식만으로는 제공할 수 없다.

3. **도구 선택 (Tool Selection)**  
   Claude는 등록된 MCP 목록 중, ‘weather-api’ 같은 도구가 필요하다고 판단한다.

4. **사용자 권한 요청 (Permission Prompt)**  
   클라이언트는 사용자에게 “외부 날씨 정보에 접근해도 될까요?”라는 식의 권한 요청을 표시한다.  
   (※ 이 단계는 사용자 프라이버시 보호를 위해 중요)

5. **요청 전송 (Information Exchange)**  
   사용자가 허용하면, 클라이언트는 해당 MCP 서버에 정해진 JSON 구조의 요청을 전송한다.

6. **외부 처리 (External Processing)**  
   MCP 서버는 실제 날씨 API를 호출하거나, DB에서 쿼리를 실행하거나, 파일을 읽는 등의 작업을 수행한다.

7. **결과 반환 (Result Return)**  
   결과는 MCP 표준 JSON 구조에 맞춰 응답된다.

8. **모델 통합 (Context Integration)**  
   Claude는 응답받은 데이터를 현재 대화 맥락에 통합한다.

9. **최종 응답 생성 (Response Generation)**  
   Claude는 자연어 형태의 답변을 만들어 사용자에게 출력한다.  
   예: “샌프란시스코는 현재 맑고 기온은 18도입니다.”

---

## 3. 클라이언트(Client)와 서버(Server) 간 역할 정리

| 구성 요소       | 주요 역할 |
|----------------|-----------|
| **MCP Client** | - 모델 내부에서 어떤 도구를 사용할지 판단<br>- `mcp.json`을 기반으로 파라미터와 요청 구조를 조립<br>- 핸드셰이크, 요청 처리, 결과 수신까지 담당 |
| **MCP Server** | - 실질적인 외부 시스템과의 통신 담당<br>- API 호출, DB 질의, 파일 읽기 등 기능 실행<br>- 결과를 JSON 포맷으로 응답 |
| **Host (실행자)** | - Claude Desktop, Cursor 등이 MCP Client를 포함한 애플리케이션<br>- 사용자 입력을 수신하고 전체 흐름을 조율<br>- 서버와 네트워크 통신, 권한 제어, 결과 출력까지 담당 |

---

## 4. Claude Desktop 기반 예시 요약

> 예시 질문: “샌프란시스코 오늘 날씨 어때?”

### 실행 흐름:

```plaintext
사용자 입력
 → Claude: 실시간 정보 필요 판단
 → Client: weather-api 도구 선택
 → Host: 사용자에게 권한 요청
 → 허용 시 MCP 서버에 요청 전송
 → 서버: 외부 API 호출 후 결과 반환
 → Claude: 결과 통합 + 자연어 응답 생성
 → 사용자에게 최종 응답 출력
