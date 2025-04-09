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

## 📚 참고 링크

- 공식 디버깅 문서: [https://modelcontextprotocol.io/docs/tools/debugging](https://modelcontextprotocol.io/docs/tools/debugging)
- MCP Inspector 사용법: [Inspector Docs](https://modelcontextprotocol.io/docs/tools/inspector)

---

> 🛠️ *이 가이드는 `04_build-your-own-mcp/` 디렉토리 내 MCP 서버 개발 시 발생할 수 있는 문제 해결을 목적으로 합니다.*
