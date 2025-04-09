# Claude Desktop에서 mcp 적용해보기
## 1. Claude Desktop 애플리케이션 다운로드 및 설치
Claude 공식 웹사이트에서 운영 체제에 맞는 설치 파일을 다운로드합니다.​
<img width="1755" alt="image" src="https://github.com/user-attachments/assets/0d864243-2aef-4239-a84d-403584bed440" />



설치 지침에 따라 애플리케이션을 설치합니다.​

이미 설치되어 있다면, 애플리케이션 메뉴에서 "Check for Updates..."를 선택하여 최신 버전인지 확인합니다.​

참고: MCP 서버는 로컬에서 실행되므로, 현재 MCP는 데스크톱 호스트에서만 지원됩니다.​

---

## 2. MCP 서버 추가 (Filesystem 예시)

Claude Desktop에서 MCP 서버를 추가하는 방법은 크게 두 가지가 있습니다:


![image](https://github.com/user-attachments/assets/8693c772-ea94-4a61-b17d-e077dfcf5deb)

### ① 사전 구축된 MCP 서버 활용하기

다음은 로컬 파일 시스템에 접근할 수 있는 Filesystem MCP 서버 예시입니다.

**설정 방법:**

- Claude 메뉴에서 **"Settings..."** → **"Developer"** → **"Edit Config"**를 클릭합니다.
- 설정 파일이 다음 위치에 열립니다:
  - **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
  - **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

설정 파일에 아래 내용을 추가합니다:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/사용자명/Desktop",
        "/Users/사용자명/Downloads"
      ]
    }
  }
}
"사용자명"을 실제 컴퓨터의 사용자명으로 변경해주세요.

경로는 자유롭게 추가하거나 변경할 수 있습니다.

### ② MCP Market에서 기존 MCP 가져오기 (ex. Smithery)

<img width="882" alt="image" src="https://github.com/user-attachments/assets/388ddb7e-841f-4465-baa9-591968d1cc44" />

Smithery와 같은 MCP 마켓에서 다른 사용자가 미리 정리한 MCP 서버를 쉽게 가져올 수 있습니다.

Smithery 같은 MCP 마켓에서 원하는 MCP 서버를 선택합니다.

> > 🔍 참고: `02_use-existing-mmcps/mcp-hub-guide.md` 파일에 **Smithery 및 기타 MCP 마켓 정보**가 정리되어 있으니 함께 참고해 주세요.


일부 MCP 서버는 API 키가 필요할 수 있습니다.

API 키가 필요한 MCP 서버는 해당 사이트에서 개인 API 키를 발급받아야 합니다.

<img width="541" alt="image" src="https://github.com/user-attachments/assets/9c9df30a-5208-4c29-9ed2-727273dfa78e" />


API 키 입력 후, Smithery 등에서는 자동으로 JSON 설정파일을 생성해줍니다.

생성된 JSON 내용을 복사하여 Claude Desktop의 설정 파일에 추가하면 됩니다.

예시 (자동 생성된 설정 파일 형태):

**예시 설정:**

```json
{
  "mcpServers": {
    "weather-api": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-weather"
      ],
      "env": {
        "WEATHER_API_KEY": "여기에_발급받은_API키를_입력하세요"
      }
    }
  }
}

JSON 파일을 설정 파일에 붙여 넣고 저장합니다.


---


## 3. Node.js 설치 확인
Filesystem과 같은 MCP 서버는 Node.js 환경이 필요합니다.

터미널(macOS) 또는 명령 프롬프트(Windows)에서 다음 명령을 실행하여 설치 여부를 확인합니다.

<img width="563" alt="image" src="https://github.com/user-attachments/assets/40f8baee-22ee-4883-8b1a-cb8607d1586f" />

설치되지 않았다면 Node.js 공식 웹사이트에서 설치합니다.


---

## 4. Claude Desktop 재시작 및 확인
설정 파일을 저장한 후, Claude Desktop을 재시작합니다.

![image](https://github.com/user-attachments/assets/6c35f057-3464-4826-b3da-41ba704559de)


입력 창 우측 하단의 망치 모양 아이콘을 클릭하여 추가된 MCP 서버의 기능을 확인할 수 있습니다.

예시 작업 요청:

"데스크톱에 파일 생성해줘"

"다운로드 폴더의 파일 목록을 보여줘"

Claude는 작업 수행 전 사용자의 확인을 요청합니다.

## 5. 문제 해결
MCP 서버가 제대로 작동하지 않을 경우 다음 사항을 점검합니다:

Claude Desktop을 재시작합니다.

JSON 설정 파일에 문법 오류가 없는지 확인합니다.

MCP 서버 경로가 정확한지 확인합니다.

MCP 서버를 수동으로 실행하여 문제를 점검합니다.

bash
npx -y @modelcontextprotocol/server-filesystem /Users/사용자명/Desktop
Claude 로그 파일에서 오류를 확인합니다:

macOS: ~/Library/Logs/Claude/mcp.log

Windows: %APPDATA%\Claude\logs\mcp.log

> > 🔍 참고: '04_build_your_own_mcp/debugging_guide.md' 파일에 **Smithery 및 기타 MCP 마켓 정보**가 정리되어 있으니 함께 참고해 주세요.

위의 절차를 따르면 손쉽게 Claude Desktop에서 MCP 서버를 설정하고 업무 자동화에 활용할 수 있습니다.

