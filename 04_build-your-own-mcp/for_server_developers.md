
## MCP 서버 구축 기본 단계

### 1. 준비물

- Node.js가 설치된 컴퓨터
- 기본적인 JavaScript 지식
- 텍스트 에디터 (VS Code 추천)

### 2. 프로젝트 설정하기

먼저 새 폴더를 만들고 터미널에서 다음 명령어를 실행합니다:

```bash
mkdir mcp-server
cd mcp-server
npm init -y
npm install @anthropic-ai/mcp
```

### 3. 기본 서버 만들기

`index.js` 파일을 생성하고 다음 코드를 작성합니다:

```javascript
const { createServer } = require('@anthropic-ai/mcp');

// MCP 서버 생성
const server = createServer({
  functions: {
    // 여기에 함수를 정의합니다
    hello_world: {
      description: "간단한 인사 함수",
      parameters: {
        type: "object",
        properties: {
          name: {
            type: "string",
            description: "인사할 사람의 이름"
          }
        },
        required: ["name"]
      },
      handler: async ({ name }) => {
        return `안녕하세요, ${name}님!`;
      }
    }
  }
});

// 서버 시작
server.listen(3000, () => {
  console.log('MCP 서버가 3000번 포트에서 실행 중입니다!');
});
```

### 4. 서버 실행하기

터미널에서 다음 명령어를 실행합니다:

```bash
node index.js
```

이제 3000번 포트에서 MCP 서버가 실행됩니다!

## 실용적인 함수 추가하기

날씨 정보를 제공하는 함수를 추가해 봅시다:

```javascript
// ...기존 코드 유지...

const server = createServer({
  functions: {
    hello_world: {
      // 기존 함수 코드...
    },
    
    get_weather: {
      description: "특정 도시의 날씨 정보를 가져옵니다",
      parameters: {
        type: "object",
        properties: {
          city: {
            type: "string",
            description: "날씨를 알고 싶은 도시 이름"
          }
        },
        required: ["city"]
      },
      handler: async ({ city }) => {
        // 실제로는 여기서 날씨 API를 호출하여 정보를 가져와야 합니다
        // 예시로 간단히 구현합니다
        return `${city}의 오늘 날씨는 맑고 기온은 22°C입니다.`;
      }
    }
  }
});

// ...서버 실행 코드...
```

## 외부 API 연동하기

실제 날씨 API를 사용해 봅시다:

```javascript
const { createServer } = require('@anthropic-ai/mcp');
const axios = require('axios'); // API 호출을 위해 axios 설치 필요 (npm install axios)

const server = createServer({
  functions: {
    get_real_weather: {
      description: "실제 날씨 API를 사용하여 날씨 정보를 가져옵니다",
      parameters: {
        type: "object",
        properties: {
          city: {
            type: "string",
            description: "날씨를 알고 싶은 도시 이름"
          }
        },
        required: ["city"]
      },
      handler: async ({ city }) => {
        try {
          // 무료 날씨 API 사용 (실제 사용시 API 키 필요할 수 있음)
          const response = await axios.get(`https://api.weatherapi.com/v1/current.json?key=YOUR_API_KEY&q=${city}`);
          
          const weatherData = response.data;
          return `${city}의 현재 날씨는 ${weatherData.current.condition.text}이고, 기온은 ${weatherData.current.temp_c}°C입니다.`;
        } catch (error) {
          return `날씨 정보를 가져오는 중 오류가 발생했습니다: ${error.message}`;
        }
      }
    }
  }
});

server.listen(3000, () => {
  console.log('MCP 서버가 3000번 포트에서 실행 중입니다!');
});
```

## 파일 시스템 접근 함수 추가하기

로컬 파일을 읽고 쓰는 함수를 추가해 봅시다:

```javascript
const { createServer } = require('@anthropic-ai/mcp');
const fs = require('fs').promises;

const server = createServer({
  functions: {
    read_file: {
      description: "지정된 파일의 내용을 읽습니다",
      parameters: {
        type: "object",
        properties: {
          filePath: {
            type: "string",
            description: "읽을 파일의 경로"
          }
        },
        required: ["filePath"]
      },
      handler: async ({ filePath }) => {
        try {
          const content = await fs.readFile(filePath, 'utf-8');
          return content;
        } catch (error) {
          return `파일 읽기 오류: ${error.message}`;
        }
      }
    },
    
    write_file: {
      description: "지정된 파일에 내용을 씁니다",
      parameters: {
        type: "object",
        properties: {
          filePath: {
            type: "string",
            description: "쓸 파일의 경로"
          },
          content: {
            type: "string",
            description: "파일에 쓸 내용"
          }
        },
        required: ["filePath", "content"]
      },
      handler: async ({ filePath, content }) => {
        try {
          await fs.writeFile(filePath, content, 'utf-8');
          return `${filePath}에 내용을 성공적으로 저장했습니다.`;
        } catch (error) {
          return `파일 쓰기 오류: ${error.message}`;
        }
      }
    }
  }
});

server.listen(3000, () => {
  console.log('MCP 서버가 3000번 포트에서 실행 중입니다!');
});
```

## 응용: 간단한 메모 애플리케이션 서버

다양한 기능을 합쳐 메모 앱 서버를 만들어 봅시다:

```javascript
const { createServer } = require('@anthropic-ai/mcp');
const fs = require('fs').promises;
const path = require('path');

const MEMOS_DIR = path.join(__dirname, 'memos');

// memos 디렉토리 생성 (없는 경우)
async function ensureMemosDir() {
  try {
    await fs.mkdir(MEMOS_DIR, { recursive: true });
  } catch (error) {
    console.error('디렉토리 생성 중 오류:', error);
  }
}

ensureMemosDir();

const server = createServer({
  functions: {
    create_memo: {
      description: "새 메모를 생성합니다",
      parameters: {
        type: "object",
        properties: {
          title: {
            type: "string",
            description: "메모 제목"
          },
          content: {
            type: "string",
            description: "메모 내용"
          }
        },
        required: ["title", "content"]
      },
      handler: async ({ title, content }) => {
        try {
          const fileName = `${title.replace(/[^가-힣a-zA-Z0-9]/g, '_')}.txt`;
          const filePath = path.join(MEMOS_DIR, fileName);
          await fs.writeFile(filePath, content, 'utf-8');
          return `"${title}" 메모가 성공적으로 저장되었습니다.`;
        } catch (error) {
          return `메모 저장 오류: ${error.message}`;
        }
      }
    },
    
    list_memos: {
      description: "모든 메모 목록을 가져옵니다",
      parameters: {
        type: "object",
        properties: {},
        required: []
      },
      handler: async () => {
        try {
          const files = await fs.readdir(MEMOS_DIR);
          if (files.length === 0) {
            return "저장된 메모가 없습니다.";
          }
          
          const memosList = files.map(file => {
            return file.replace('.txt', '').replace(/_/g, ' ');
          });
          
          return `메모 목록:\n${memosList.join('\n')}`;
        } catch (error) {
          return `메모 목록 가져오기 오류: ${error.message}`;
        }
      }
    },
    
    read_memo: {
      description: "특정 메모 내용을 읽습니다",
      parameters: {
        type: "object",
        properties: {
          title: {
            type: "string",
            description: "읽을 메모 제목"
          }
        },
        required: ["title"]
      },
      handler: async ({ title }) => {
        try {
          const fileName = `${title.replace(/[^가-힣a-zA-Z0-9]/g, '_')}.txt`;
          const filePath = path.join(MEMOS_DIR, fileName);
          const content = await fs.readFile(filePath, 'utf-8');
          return `제목: ${title}\n\n${content}`;
        } catch (error) {
          return `메모 읽기 오류: ${error.message}`;
        }
      }
    }
  }
});

server.listen(3000, () => {
  console.log('메모 앱 MCP 서버가 3000번 포트에서 실행 중입니다!');
});
```

## 마치며

이제 기본적인 MCP 서버를 구축하는 방법을 배웠습니다! 여기서 더 발전시켜 다양한 기능을 추가할 수 있습니다:

1. 데이터베이스 연동 (MongoDB, MySQL 등)
2. 다양한 외부 API 연동 (번역, 이미지 생성 등)
3. 복잡한 데이터 처리 기능 추가
