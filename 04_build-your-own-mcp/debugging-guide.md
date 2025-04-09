# MCP 디버깅 가이드

이 문서는 MCP(Model Context Protocol) 서버/클라이언트를 개발하거나 테스트하는 과정에서 발생할 수 있는 **연결 문제(disconnect)**, **스키마 오류**, **환경 변수 문제** 등을 해결하기 위한 **실용 디버깅 가이드**입니다.  
🔧 초보자도 따라할 수 있도록 단계별로 정리했습니다.

---

## ✅ 1. Claude Desktop에서 MCP 디버깅하기

### 📍 1-1. MCP 로그 확인

Claude Desktop은 MCP 관련 로그를 별도로 기록합니다.  
터미널에서 다음 명령어를 실행하면 실시간 로그를 볼 수 있습니다:

```bash
tail -n 20 -F ~/Library/Logs/Claude/mcp*.log
```

> 📝 이 로그에는 서버 연결, 초기화 실패, 스키마 오류 등 중요한 정보가 포함되어 있습니다.

---

### 🧰 1-2. 개발자 도구(DevTools) 활성화

Claude의 내부 구조를 보기 위해 Chrome DevTools를 사용할 수 있습니다.

#### 설정 방법:

```bash
echo '{"allowDevTools": true}' > ~/Library/Application\ Support/Claude/developer_settings.json
```

이후 Claude 실행 상태에서 다음 단축키 입력:

```
Command + Option + Shift + I
```

- 두 개의 DevTools 창이 뜹니다 (메인 콘텐츠 / 타이틀 바)
- `Console`, `Network` 탭을 통해 메시지 흐름 및 오류 확인 가능

---

## 🚦 2. 서버 연결 오류가 날 때 확인할 것

### 🧱 2-1. 절대 경로 사용

`claude_desktop_config.json`에서 상대 경로 대신 항상 절대 경로를 사용하세요.

```json
{
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/username/data"]
}
```

---

### 🔐 2-2. 환경 변수 누락

MCP 서버는 기본적으로 `USER`, `HOME`, `PATH`만 상속합니다.  
추가 변수가 필요하면 아래처럼 명시해야 합니다:

```json
{
  "myserver": {
    "command": "mcp-server-myapp",
    "env": {
      "MYAPP_API_KEY": "some_key"
    }
  }
}
```

---

### 🧪 2-3. 단독 실행 테스트 (권장)

[MCP Inspector](https://modelcontextprotocol.io/docs/tools/inspector)를 사용하면  
Claude 없이 MCP 서버를 테스트할 수 있습니다.

---

## 📌 3. 자주 발생하는 연결 오류

| 문제 | 원인 | 해결 방법 |
|------|------|-----------|
| 서버 연결 후 바로 끊김 | 스키마 오류 | `mcp.json` 구조 점검, 필수 필드 누락 여부 확인 |
| 연결은 되는데 작동 안됨 | capability 미등록 | `.json`의 `resources`, `prompts` 정의 여부 확인 |
| Permission prompt 안 뜸 | 클라이언트와 버전 불일치 | Claude 최신 버전인지 확인 |
| "Cannot execute" 오류 | 실행 권한 없음 | 서버 스크립트에 실행 권한 부여 (`chmod +x`) |





---
네, 미소님! MCP 디버깅을 **터미널 환경에서만** 효과적으로 수행하는 방법도 꽤 많습니다. GUI 없이도 로그 추적, 서버 상태 확인, 요청 시뮬레이션 등을 충분히 할 수 있어요. 아래에 실무용으로 유용한 터미널 중심 디버깅 방법을 정리해 드릴게요.  

---

## ✅ 1. MCP 로그 실시간 확인

```bash
tail -n 50 -F ~/Library/Logs/Claude/mcp*.log
```

- Claude에서 MCP 관련 문제가 발생하면, 이 로그 파일에 거의 대부분의 힌트가 남습니다.
- 연결 시도, 오류, 끊김 시점 등이 시간 순으로 찍혀요.

---

## ✅ 2. MCP 서버 단독 실행 + 로그 추적

### 예시 (Node.js MCP 서버 기준):

```bash
DEBUG=mcp* node src/server.js
```

- `DEBUG=mcp*`는 MCP 관련 내부 로직 흐름을 터미널에서 볼 수 있도록 하는 환경변수예요.
- 실행 중 어떤 요청이 왔고, 어떤 이유로 실패했는지 확인 가능.

또는 TypeScript 기반이면:

```bash
npm run dev  # ts-node 기반 실행
```

---

## ✅ 3. 요청 직접 테스트 (cURL 또는 HTTPie)

```bash
curl -X POST http://localhost:PORT \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getWeather",
    "params": { "city": "Seoul" },
    "id": 1
}'
```

- Claude 없이도 MCP 서버가 잘 응답하는지 확인 가능.
- `jsonrpc`, `method`, `params`가 올바르게 들어갔는지도 확인할 수 있어요.

---

## ✅ 4. MCP Inspector 로컬 실행

```bash
npx @modelcontextprotocol/inspector
```

- Inspector는 Claude 없이도 MCP 서버를 호출해볼 수 있는 터미널 기반 도구예요.
- 실행 후 localhost에 브라우저가 열리지만, 내부 구조는 Node 기반 CLI로도 동작합니다.

---

## ✅ 5. 프로세스 로그 보기 (백그라운드 MCP 서버 디버깅)

```bash
ps aux | grep mcp
```

```bash
lsof -i :PORT  # 해당 포트 점유 프로세스 확인
```

- MCP 서버가 예상대로 실행 중인지 확인하거나, 포트 충돌 여부를 추적할 수 있어요.

---

## ✅ 6. MCP JSON 스키마 유효성 검사

```bash
jq . mcp.json  # JSON 구조 유효성만 간단히 확인
```

혹은 더 엄격하게 검증하려면 JSON Schema Validator 사용:

```bash
npx jsonschema -i mcp.json schema.json
```

---

## 🔄 요약: 순서대로 디버깅 시나리오

1. `tail -f`로 Claude 로그 실시간 확인
2. MCP 서버 직접 `DEBUG=mcp*` 옵션으로 실행
3. `curl` 또는 Inspector로 요청 보내보기
4. `mcp.json` 구조 점검 (`jq`, `jsonschema`)
5. 포트 충돌, 프로세스 상태 확인 (`lsof`, `ps`, `kill`)
6. 필요시 DevTools 또는 브라우저로 MCP Inspector 사용

---



## 📚 참고 링크

- 공식 디버깅 문서: [https://modelcontextprotocol.io/docs/tools/debugging](https://modelcontextprotocol.io/docs/tools/debugging)
- MCP Inspector 사용법: [Inspector Docs](https://modelcontextprotocol.io/docs/tools/inspector)

---

> 🛠️ *이 가이드는 `04_build-your-own-mcp/` 디렉토리 내 MCP 서버 개발 시 발생할 수 있는 문제 해결을 목적으로 합니다.*



