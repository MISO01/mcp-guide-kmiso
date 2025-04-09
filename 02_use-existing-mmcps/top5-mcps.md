이 문서는 Claude, Cursor 등 다양한 LLM 기반 클라이언트에서 자주 활용되는 MCP 서버 중 실용성과 활용도가 높은 5개 MCP를 선정하여, 각 기능 설명과 함께 실사용 시나리오를 제공하는 정리 문서입니다.



# 1. Sequential Thinking MCP Tool

<img width="881" alt="image" src="https://github.com/user-attachments/assets/0e5b02b2-0a19-4648-850a-776e21f417f3" />
`sequentialthinking`은 복잡한 문제를 단계적으로 구조화하여 사고 과정을 명확히 하고, 반복/수정 가능한 구조로 문제 해결을 지원하는 MCP 도구입니다. 각 단계(thought)는 이전 생각을 기반으로 확장하거나 수정할 수 있도록 설계되어 있음.

---

### ✅ 주요 사용 목적

- 복잡한 문제를 단계별로 분해하고자 할 때
- 기획 또는 설계 중 수정이 자주 발생하는 경우
- 사고 도중 방향 전환이 필요한 분석
- 문제 범위가 처음부터 명확하지 않은 경우
- 다단계 솔루션 설계 필요 시
- 여러 단계에 걸쳐 맥락을 유지해야 하는 작업
- 불필요한 정보를 걸러내야 하는 상황

---

### 🛠️ 기능 요약

- 사고 단계 수(total_thoughts)를 유동적으로 조절 가능
- 이전 thought에 대해 질문하거나 수정 가능
- 종료 이후에도 새로운 thought 추가 가능
- 불확실성 표현 및 대안 경로 분기/되돌리기(backtrack) 가능
- 해결 가설(hypothesis) 생성 및 검증
- 최종 결과 도출 전까지 반복적 사고 구조 지원

---

### 🔣 사용 가능한 파라미터 설명

- `thought`: 현재 사고 단계에서 작성한 내용
  - 일반적인 분석 과정, 이전 thought에 대한 질문, 추가 정보 확인, 접근 방식 변경, 가설 제안 등 다양한 형식 가능
- `next_thought_needed`: 생각이 더 필요한 경우 true로 설정
- `thought_number`: 현재 사고 단계 번호. 총 단계 수를 초과할 수도 있음
- `total_thoughts`: 예상되는 전체 사고 단계 수 (진행 중 변경 가능)
- `is_revision`: 이전 thought를 수정하는 경우 true
- `revises_thought`: 수정하려는 thought 번호
- `branch_from_thought`: 분기를 시작하는 지점의 thought 번호
- `branch_id`: 현재 분기에 대한 식별자 (선택 사항)
- `needs_more_thoughts`: 마지막 단계처럼 보이지만 실제로는 더 생각이 필요할 경우 true

---

### 🔁 사용 패턴 가이드

1. 예상 단계 수를 설정하고 시작하되, 필요에 따라 유연하게 수정할 것
2. 기존 thought에 대해 자유롭게 질문하거나 수정 가능
3. "끝"처럼 보여도 생각이 더 필요하면 계속 추가 가능
4. 불확실하거나 애매한 내용은 그대로 기록해도 좋음
5. 가설이 필요하면 명시적으로 생성하고 이후 단계에서 검증
6. 이전 내용을 수정하거나 새로운 방향으로 분기할 경우 관련 파라미터 지정
7. 불필요한 정보는 명확히 제외
8. 최종 결과는 단일하고 검증 가능한 형태로 도출할 것
9. 최종 thought까지 완료되었고 만족스러운 결과가 나왔을 때만 `next_thought_needed`를 false로 설정

---

> 이 MCP 도구는 AI가 단순한 질의응답을 넘어 구조화된 사고 흐름을 따라 복잡한 문제를 다룰 수 있도록 설계됨. 특히 계획, 전략 수립, 의사결정 프레임 정리에 적합.


## 🔧 SequentialThinking MCP 서버 설치

이 도구를 직접 실행하려면 공식 레퍼런스 서버를 활용하세요:

- GitHub: [Smithery Reference Server - SequentialThinking](https://github.com/smithery-ai/reference-servers/tree/main/src/sequentialthinking)

해당 리포지토리는 다음을 포함합니다:

- Python 기반 MCP 서버 구현
- `mcp.json` 사양 정의 및 예시
- 로컬 테스트용 실행 스크립트
- 커스텀 툴 파라미터 수정 예제






---

# 2. Desktop Commander MCP
<img width="881" alt="image" src="https://github.com/user-attachments/assets/166f729d-66d1-4ffc-9a3b-8a2ba743011e" />



> 🔗 GitHub: [wonderwhy-er/DesktopCommanderMCP](https://github.com/wonderwhy-er/DesktopCommanderMCP)

로컬 터미널 명령어를 안전하게 실행하고 관리할 수 있도록 지원하는 MCP 서버입니다. Claude나 Cursor 같은 클라이언트에서 로컬 쉘 환경과 상호작용할 수 있게 하며, **파일 시스템, 프로세스, 터미널 세션 제어** 등을 통합 관리할 수 있습니다.

## 주요 기능 및 사용 사례

---

#### 🧪 execute_command
- 설명: 지정된 명령어를 타임아웃과 함께 실행. 시간이 초과되면 백그라운드에서 계속 실행 가능.
- 사용 예: `ls`, `npx`, `python run.py` 같은 명령어를 Claude로 실행할 때.


#### 📥 read_output
- 설명: 특정 터미널 세션의 출력 결과를 읽음.
- 사용 예: 백그라운드에서 실행 중인 명령의 로그를 Claude가 확인할 때.


#### 💣 force_terminate
- 설명: 지정된 세션을 강제로 종료.
- 사용 예: 오작동하거나 무한루프에 빠진 명령을 차단하고 싶을 때.


#### 📜 list_sessions
- 설명: 현재 활성화된 터미널 세션 목록을 반환.
- 사용 예: 어떤 명령이 실행 중인지 AI가 확인하고 판단을 내릴 때.


#### 🧠 list_processes
- 설명: 전체 프로세스를 나열. PID, 명령어, CPU·메모리 사용량 포함.
- 사용 예: Claude가 리소스 모니터링이나 비정상 프로세스를 탐지하는 데 활용 가능.


#### 🗡 kill_process
- 설명: PID 기준으로 프로세스를 강제 종료.
- 사용 예: 리소스를 과다하게 사용하는 프로세스를 Claude가 차단할 때.


#### ⛔ block_command / ✅ unblock_command
- 설명: 특정 명령어를 차단하거나 차단 해제.
- 사용 예: `rm -rf /` 같은 위험한 명령을 사전에 차단.


#### 🧾 list_blocked_commands
- 설명: 현재 차단된 명령어 리스트 조회.
- 사용 예: 시스템 보호 정책을 점검할 때.


#### 📄 read_file / 📚 read_multiple_files
- 설명: 텍스트 파일 내용 전체 읽기, 다중 파일 동시 처리 가능.
- 사용 예: Claude가 로컬 메모나 설정 파일을 분석할 때.


#### ✍️ write_file
- 설명: 파일 내용을 전부 대체. 20% 이상 변경 시 적합.
- 사용 예: `.env`, `config.json` 등을 Claude가 업데이트할 때.


#### 🗂 create_directory / 📂 list_directory
- 설명: 폴더 생성, 경로 내 파일·폴더 리스트 확인.
- 사용 예: Claude가 새 폴더 구조를 만들거나, 특정 디렉토리 내용 스캔 시.


#### 📦 move_file
- 설명: 파일 또는 폴더를 이동 또는 이름 변경.
- 사용 예: Claude가 백업 작업 중에 폴더를 옮기거나 정리할 때.


#### 🔍 search_files / search_code
- 설명: 경로 내 파일명/코드 내용 검색 (ripgrep 기반).
- 사용 예: Claude가 대규모 프로젝트에서 특정 함수나 키워드를 검색할 때.


#### 🧾 get_file_info
- 설명: 파일/폴더의 메타 정보 조회. 생성일, 수정일, 권한 등 포함.
- 사용 예: Claude가 문서 버전이나 최근 수정 여부를 판단할 때.


#### ✅ list_allowed_directories
- 설명: 현재 MCP 서버가 접근 허용된 디렉토리 목록 조회.
- 사용 예: Claude가 파일 접근 가능 경로를 사전 확인할 때.


#### 🧬 edit_block
- 설명: 파일 내 일부 텍스트만 정밀 변경 (<20% 크기 변경 시).
- 사용 예: 긴 파일을 전체 수정하지 않고, 일부 블록만 바꿔야 할 때.


### 💡 추천 사용 환경

- Claude 기반 에이전트를 로컬 시스템에 연결하고 싶은 사용자
- 실시간 파일 수정, 로그 분석, 자동화 작업이 필요한 개발자
- Claude나 Cursor에서 “로컬 터미널 명령 실행” 기능을 구현하고자 하는 AI 워크플로우 설계자

---

> 📌 참고: 해당 MCP는 보안 설정(허용된 디렉토리, 차단 명령 리스트 등)을 통해 시스템을 안전하게 유지하는 것을 전제로 설계되었습니다.


---





# 3. GITHUB Mcp Server

<img width="881" alt="image" src="https://github.com/user-attachments/assets/27424394-a9db-48ca-86c8-a6a27438ab2a" />


이 문서는 [`smithery-ai/github`](https://github.com/smithery-ai/reference-servers/tree/main/src/github) MCP 서버의 기능을 상세히 정리한 자료입니다. 기능 설명은 실제 MCP 호출 기준이며, **언제 이 기능을 활용하면 좋은지**를 예시와 함께 제공합니다.

---

### ✅ `create_or_update_file`
**설명:**
GitHub 리포지토리의 단일 파일을 생성하거나 업데이트합니다. 기존 파일이 존재하면 덮어쓰고, 없으면 새로 생성합니다.

**추천 사용 상황:**
- README, `.md` 문서 등을 자동 생성할 때
- Claude 또는 GPT가 자동 생성한 분석 결과를 깃헙 문서로 남길 때



### 🔍 `search_repositories`
**설명:**
GitHub에서 키워드를 기준으로 공개 저장소를 검색합니다.

**추천 사용 상황:**
- 특정 주제(예: `openpose`, `mcp`, `nextjs`)에 대한 유명 프로젝트 검색
- 자동화 워크플로우에서 오픈소스 레퍼런스 검색시 사용


### 📁 `create_repository`
**설명:**
지정된 이름으로 새로운 GitHub 저장소를 생성합니다. 개인 또는 조직 계정에 생성 가능.

**추천 사용 상황:**
- AI가 새 프로젝트 시작하면서 초기 리포 자동 생성할 때
- 팀 협업용 임시 리포 자동 생성 시나리오


### 📄 `get_file_contents`
**설명:**
리포지토리 내 특정 파일이나 디렉토리의 내용을 가져옵니다.

**추천 사용 상황:**
- 분석 대상 소스코드 파일을 Claude가 읽어야 할 때
- README, 설정 파일, 코드 일부를 LLM에게 제공하기 위한 사전 맥락 수집용



### 🚀 `push_files`
**설명:**
여러 파일을 한 번에 커밋하여 리포지토리에 푸시합니다.

**추천 사용 상황:**
- 자동화된 결과물 (예: 코드 수정, 문서 번역 등) 일괄 커밋
- ChatOps에서 작업 로그를 자동으로 기록하고자 할 때



### 🐞 `create_issue`
**설명:**
리포지토리에 새로운 이슈를 생성합니다. 제목과 내용을 포함합니다.

**추천 사용 상황:**
- 버그 리포트나 기능 요청을 자동 등록할 때
- Claude가 코드 리뷰 후 개선점 이슈 자동 등록 시


### 🔀 `create_pull_request`
**설명:**
지정된 브랜치에서 PR을 생성합니다. 병합 대상, 제목, 설명 포함 가능.

**추천 사용 상황:**
- AI가 자동 수정한 브랜치를 메인으로 병합하려 할 때
- LLM이 작성한 코드에 대한 사용자 확인을 받고 싶을 때


### 🍴 `fork_repository`
**설명:**
다른 사용자의 리포지토리를 내 계정 또는 조직으로 포크합니다.

**추천 사용 상황:**
- 공개 프로젝트 기반으로 개인 버전 만들 때
- 실험용으로 원본 코드 변경 없이 테스트하고 싶을 때


### 🌿 `create_branch`
**설명:**
지정된 리포지토리에 새로운 브랜치를 생성합니다.

**추천 사용 상황:**
- AI가 여러 실험적 수정을 분기해서 수행할 때
- 버그 수정/기능 개발을 브랜치 기반으로 분리하고자 할 때


### 🧾 `list_commits`
**설명:**
지정한 브랜치의 커밋 목록을 조회합니다. 커밋 메시지, 작성자, 시간 등 포함.

**추천 사용 상황:**
- 코드 변경 이력 분석 (예: Claude에게 최근 변경사항 설명 시)
- 특정 기능의 변경 내역 추적


### 📋 `list_issues`
**설명:**
리포지토리의 이슈 목록을 필터링 조건과 함께 가져옵니다.

**추천 사용 상황:**
- 현재 열려 있는 버그/기능 요청 이슈 요약 시
- 자동 회의록 작성 시 이슈 목록 정리 용도


### 🔧 `update_issue`
**설명:**
기존 이슈의 제목, 본문, 상태 등을 업데이트합니다.

**추천 사용 상황:**
- Claude가 이슈의 상태를 자동으로 닫거나 업데이트할 때
- 작업 후 코멘트나 설명 추가가 필요할 때


### 💬 `add_issue_comment`
**설명:**
기존 이슈에 댓글을 추가합니다.

**추천 사용 상황:**
- Claude가 리뷰 결과를 이슈 코멘트로 남길 때
- Slack에서 받은 피드백을 자동으로 이슈 코멘트로 변환 시


### 🔍 `search_code`
**설명:**
GitHub 전체에서 키워드 기반 코드 검색을 수행합니다. 예: 특정 함수, API 사용 예시 등.

**추천 사용 상황:**
- 특정 라이브러리 사용 예를 찾아 Claude에게 학습시키고 싶을 때
- 리팩토링 대상 함수의 다른 구현을 조사할 때


### 🧩 `search_issues`
**설명:**
GitHub 전체에서 특정 키워드와 관련된 이슈/PR 검색을 수행합니다.

**추천 사용 상황:**
- 유명 프로젝트에서 관련된 토론 내역 탐색
- 기존에 해결된 버그인지 확인하고자 할 때


### 👤 `search_users`
**설명:**
GitHub 사용자 계정 검색. 사용자명, 닉네임 기반.

**추천 사용 상황:**
- 오픈소스 기여자 또는 유지관리자 탐색
- PR 작성자 확인, 커뮤니티 분석 등


### 🧾 `get_issue`
**설명:**
특정 이슈의 상세 내용을 가져옵니다. 제목, 본문, 상태, 라벨 등 포함.

**추천 사용 상황:**
- Claude가 이슈 내용을 요약하거나 분석할 때
- 회의 시 이슈별 상세 정보를 출력하고자 할 때

---

## 📦 GitHub MCP 서버 링크
- GitHub 서버 Repo: https://github.com/smithery-ai/reference-servers/tree/main/src/github




---

# 4. brave search MCP
<img width="881" alt="image" src="https://github.com/user-attachments/assets/1c9bec77-d3e2-41c1-96f7-78ad84fbf9f0" />

- GitHub: [smithery-ai/reference-servers › brave-search](https://github.com/smithery-ai/reference-servers/tree/main/src/brave-search)
- Type: `Remote`
- 서버 위치: Smithery 플랫폼 (`@smithery-ai/brave-search`)

---

#### 🧰 제공 기능

##### `brave_web_search`
Brave Search API를 활용한 일반 웹 검색 기능.
- 뉴스, 문서, 최근 이슈, 블로그 등 공개된 콘텐츠를 대상으로 검색
- 최대 20개의 결과 제공 (페이지네이션 지원)
- 필터링, freshness control, 콘텐츠 타입 제한 등 지원

**예시 사용 시나리오:**
- 최신 AI 관련 논문 보도 검색
- 최근 산업 트렌드 요약 요청 (Claude에서 자동 요약 포함)

##### `brave_local_search`
Brave의 Local Search API 기반 지역 정보 검색.
- 장소명, 주소, 전화번호, 운영 시간, 평점 등 포함
- 'OO 근처', '강남 스시 맛집'처럼 위치 기반 쿼리에 최적화
- 로컬 결과 없을 시 자동으로 웹 검색 fallback 수행

**예시 사용 시나리오:**
- “서울 신촌 근처 브런치 맛집 찾아줘” → 상세 장소 정보 포함 반환
- “OO역 근처 약국 운영시간 알려줘” → 실제 전화번호와 시간 포함 응답

---

#### 🤖 언제 쓰면 좋은가?

| 상황 | 활용 예시 |
|------|-----------|
| 📄 뉴스/정보 자동 수집 | Claude가 `brave_web_search` 호출로 다양한 언론 기사 요약 |
| 🍽️ 장소 추천 | 로컬 검색 기반 맛집/시설 안내 → Claude나 Agent의 음성/챗 인터페이스에 유용 |
| 🧠 분석형 워크플로우 조합 | `sequentialthinking` MCP와 결합해 검색 결과에 기반한 단계적 사고 흐름 설계 |
| 🔗 외부 API 연계 전 테스트 | RAG 시스템 구축 전 공개 검색 API로 임시 데이터 활용 |

---

#### 📌 통합 팁

- `brave_web_search` + `sequentialthinking` 조합 → 정보 수집 → 가설 구성/수정 → 최종 결론 생성
- `brave_local_search` + 지도 기반 앱 연동 → 실시간 오프라인 시설 안내에 Claude 연계 가능
- Claude Desktop, Cursor 등에서 바로 호출 가능 (Smithery 연동 기준)

---

#### 📎 출처
- GitHub Repo: https://github.com/smithery-ai/reference-servers/tree/main/src/brave-search
- Smithery MCP Server: [@smithery-ai/brave-search](https://smithery.ai/server/@smithery-ai/brave-search/tools)

- # 5. Neon MCP

- <img width="881" alt="image" src="https://github.com/user-attachments/assets/6bb19995-4878-47c5-9983-87d4160862da" />

## Neon Database MCP 서버 소개

Neon은 PostgreSQL 호환 클라우드 기반의 서버리스 데이터베이스 플랫폼으로, 스토리지와 컴퓨팅을 분리한 구조를 제공합니다. 빠르게 브랜치를 만들고, 스냅샷으로 롤백하거나 테스트 환경을 분리하는 기능이 특징입니다. 이 MCP 서버는 Neon의 다양한 기능을 LLM이나 AI Agent가 바로 호출할 수 있게 해주는 인터페이스입니다.

> GitHub 저장소: https://github.com/neondatabase-labs/mcp-server-neon

---

## 주요 기능 및 사용 예시

### ✅ 기본 정보 조회 및 프로젝트 관리

- `__node_version`: MCP 서버가 사용 중인 Node.js 버전 확인
- `list_projects`: 현재 계정에 있는 모든 Neon 프로젝트 목록 반환
- `create_project`: 새로운 Neon 프로젝트 생성
- `delete_project`: 기존 프로젝트 삭제
- `describe_project`: 프로젝트의 상세 구조 확인 (DB, 브랜치 포함)

💡 **사용 예시**: 사용자 입력을 받아 새 DB 프로젝트를 만들고, 구조를 출력해주는 대화형 워크플로우에 적합함.


### 🛠️ SQL 실행 기능

- `run_sql`: 단일 SQL 문장을 DB에 실행 (SELECT, INSERT 등)
- `run_sql_transaction`: 여러 SQL 문장을 트랜잭션 단위로 실행 (롤백/커밋 지원)

💡 **사용 예시**: "다음 SQL 문장 실행해서 결과 보여줘" 요청을 처리하거나, 데이터 삽입 자동화에 사용 가능.



### 🧩 테이블 및 스키마 관리

- `describe_table_schema`: 특정 테이블의 컬럼 구조 반환
- `get_database_tables`: DB에 존재하는 테이블 목록 조회

💡 **사용 예시**: "테이블 스키마 설명해줘" 혹은 "현재 테이블 목록 뭐 있어?" 질문에 대응.



### 🧬 브랜치 관리 기능

- `create_branch`: 기존 프로젝트에서 새로운 브랜치 생성 (테스트용 환경 분리)
- `delete_branch`: 브랜치 삭제
- `describe_branch`: 특정 브랜치의 전체 오브젝트 구조(테이블, 뷰 등) 트리로 반환

💡 **사용 예시**: 새 기능 테스트를 위한 브랜치 생성 → 데이터 조작 → 브랜치 폐기까지 전체 워크플로우 구성 가능.



### 🔁 마이그레이션 도구

- `prepare_database_migration`: 자동으로 DDL 생성하여 마이그레이션 준비 (임시 브랜치 생성)
- `complete_database_migration`: 준비된 마이그레이션을 메인 브랜치에 병합 (자동 삭제 포함)

💡 **사용 예시**: AI가 기존 DB 구조 분석 후, 자동으로 새로운 컬럼 추가 등의 마이그레이션 계획을 세우고 실행.



### 🔐 기타 도구

- `get_connection_string`: Neon DB의 PostgreSQL 연결 문자열 생성
- `provision_neon_auth`: 외부 인증 시스템과 통합을 위한 인증 설정 자동 구성 (Stack Auth 기반)

💡 **사용 예시**: Claude나 Agent가 DB 접근을 위한 연결 세팅 자동화.

---

## 추천 시나리오

- LLM이 실시간으로 Neon DB에 연결하여 데이터 질의/삽입/수정/삭제
- 사용자 입력 기반의 DB 마이그레이션 플로우 자동화
- 에이전트가 테이블 구조 분석 후 최적화 제안
- 복수 브랜치를 만들어 테스트 환경 분리 및 롤백 자동화

---

## 참고
- GitHub 저장소: [neondatabase-labs/mcp-server-neon](https://github.com/neondatabase-labs/mcp-server-neon)
- 공식 웹사이트: [https://neon.tech](https://neon.tech)

MCP 기반으로 Neon을 연동하면, AI가 직접 클라우드 DB를 다룰 수 있어 자동화된 데이터 어시스턴트나 개발 워크플로우에 매우 유용합니다.












