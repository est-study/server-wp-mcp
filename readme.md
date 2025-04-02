# WordPress MCP 서버
[![smithery 배지](https://smithery.ai/badge/server-wp-mcp)](https://smithery.ai/server/server-wp-mcp)

[Model Context Protocol (MCP)](https://modelcontextprotocol.io/) 서버로, AI 어시스턴트가 WordPress REST API를 통해 WordPress 사이트와 상호작용할 수 있게 해줍니다. 여러 개의 WordPress 사이트를 안전하게 인증하고, 콘텐츠 관리, 게시물 작업, 사이트 설정 등을 자연어로 수행할 수 있도록 지원합니다.

## 주요 기능

- **멀티 사이트 지원**: 여러 WordPress 사이트에 동시에 연결 가능  
- **REST API 통합**: WordPress REST API의 모든 엔드포인트에 접근  
- **안전한 인증 방식**: 애플리케이션 비밀번호를 이용한 안전한 API 접근  
- **동적 엔드포인트 탐색**: 각 사이트의 사용 가능한 엔드포인트 자동 매핑  
- **유연한 작업 방식**: GET, POST, PUT, DELETE, PATCH 메서드 지원  
- **에러 처리**: 명확한 메시지와 함께 우아한 오류 처리  
- **간단한 설정**: 유지보수가 쉬운 JSON 설정 파일  

## 설치 방법

### Smithery를 통한 설치

[Smithery](https://smithery.ai/server/server-wp-mcp)를 통해 Claude Desktop용 WordPress Server를 자동 설치하려면:

```bash
npx -y @smithery/cli install server-wp-mcp --client claude
```

### 수동 설치

```bash
npm install server-wp-mcp
```

## 도구 참조

### `wp_discover_endpoints`  
WordPress 사이트에서 사용 가능한 모든 REST API 엔드포인트를 매핑합니다.

**인자:**
```json
{
	"site": {
		"type": "string",
		"description": "설정에서 정의한 사이트 별칭",
		"required": true
	}
}
```

**반환값:**  
사용 가능한 엔드포인트 목록, 해당 메서드 및 네임스페이스 포함

---

### `wp_call_endpoint`  
WordPress 사이트에 REST API 요청을 수행합니다.

**인자:**
```json
{
	"site": {
		"type": "string",
		"description": "사이트 별칭",
		"required": true
	},
	"endpoint": {
		"type": "string",
		"description": "API 엔드포인트 경로",
		"required": true
	},
	"method": {
		"type": "string",
		"enum": ["GET", "POST", "PUT", "DELETE", "PATCH"],
		"description": "HTTP 메서드",
		"default": "GET"
	},
	"params": {
		"type": "object",
		"description": "요청 파라미터 또는 본문 데이터",
		"required": false
	}
}
```

---

## 설정 방법

### 애플리케이션 비밀번호 발급 방법

1. WordPress 관리자 대시보드에 로그인  
2. **Users → Profile**(사용자 → 프로필)로 이동  
3. "Application Passwords"(애플리케이션 비밀번호) 섹션으로 스크롤  
4. 애플리케이션 이름 입력 (예: "MCP Server")  
5. **"Add New Application Password"** 클릭  
6. 생성된 비밀번호를 복사 (다시 볼 수 없음)

> 참고: 애플리케이션 비밀번호는 WordPress 5.6 이상 및 HTTPS 환경에서만 사용 가능

---

### 설정 파일 구성

WordPress 사이트 정보를 포함하는 JSON 파일(`wp-sites.json` 등)을 생성합니다:

```json
{
	"myblog": {
		"URL": "https://myblog.com",
		"USER": "yourusername",
		"PASS": "abcd 1234 efgh 5678"
	},
	"testsite": {
		"URL": "https://test.example.com",
		"USER": "anotherusername",
		"PASS": "wxyz 9876 lmno 5432"
	}
}
```

사이트 설정 항목:
- `URL`: WordPress 사이트 주소 (http:// 또는 https:// 포함 필수)
- `USER`: WordPress 사용자 이름
- `PASS`: 애플리케이션 비밀번호 (공백은 자동 제거됨)

설정 키 (예: `"myblog"`, `"testsite"`)는 서버와 상호작용할 때 사용하는 사이트 별칭이 됩니다.

---

### Claude Desktop과 연동

`claude_desktop_config.json` 파일에 다음 항목 추가:

```json
{
	"mcpServers": {
		"wordpress": {
			"command": "node",
			"args": ["path/to/server/dist/index.js"],
			"env": {
				"WP_SITES_PATH": "/absolute/path/to/wp-sites.json"
			}
		}
	}
}
```

`WP_SITES_PATH` 환경 변수는 설정 파일의 **절대 경로**여야 합니다.

---

### 사용 예시

설정이 완료되면 Claude에게 다양한 WordPress 작업을 요청할 수 있습니다:

#### 게시글 목록 및 검색
```
myblog에서 지난 한 달간 발행된 모든 게시글 보여줘
```
```
testsite에서 "technology"와 "AI" 태그가 있는 게시글 찾아줘
```
```
myblog의 검토가 필요한 초안 게시글 보여줘
```

#### 콘텐츠 생성 및 수정
```
testsite에 "The Future of AI"라는 제목의 초안 게시글을 생성하고, 다음 내용을 포함해줘: [요점]
```
```
myblog의 최신 머신러닝 게시글에 대표 이미지를 업데이트해줘
```
```
myblog에 "Tech News"라는 새 카테고리 추가해줘
```

#### 댓글 관리
```
myblog의 최신 게시글에 보류 중인 댓글 모두 보여줘
```
```
testsite에서 스팸 가능성이 있는 댓글 찾아줘
```
```
myblog에서 가장 활발한 댓글 작성자 목록 보여줘
```

#### 플러그인 관리
```
myblog에 현재 활성화된 플러그인 목록 보여줘
```
```
testsite에서 업데이트가 필요한 플러그인 있는지 확인해줘
```
```
myblog에 설치된 보안 플러그인 정보 알려줘
```

#### 사용자 관리
```
testsite에서 편집자 역할을 가진 사용자 모두 보여줘
```
```
myblog에 새 작가 계정을 생성해줘
```
```
testsite에서 사용자 역할 및 권한을 업데이트해줘
```

#### 사이트 설정 및 구성
```
myblog에 현재 활성화된 테마가 뭐야?
```
```
testsite의 퍼머링크 구조를 확인해줘
```
```
myblog의 미디어 라이브러리 설정을 보여줘
```

#### 유지관리 및 진단
```
myblog에서 깨진 링크가 있는지 확인해줘
```
```
testsite의 PHP 버전 및 시스템 정보를 보여줘
```
```
myblog에서 보류 중인 데이터베이스 업데이트 목록 알려줘
```

---

## 에러 처리

서버는 다음과 같은 일반적인 오류를 처리합니다:
- 잘못된 설정 파일 경로 또는 포맷
- 유효하지 않은 사이트 설정
- 인증 실패
- 존재하지 않거나 잘못된 엔드포인트
- API 요청 제한
- 네트워크 오류

모든 오류는 문제 해결을 위한 명확한 메시지와 함께 반환됩니다.

---

## 보안 고려사항

- `wp-sites.json` 파일은 안전하게 보관하고, 절대 버전 관리(Git 등)에 포함시키지 마세요
- 민감한 데이터는 프로덕션 환경에서는 환경 변수로 관리하는 것을 권장합니다
- 설정 파일은 공개된 디렉터리 외부에 위치시켜야 합니다
- 모든 WordPress 사이트는 HTTPS를 사용해야 합니다
- 애플리케이션 비밀번호는 주기적으로 변경하세요
- 사용자 권한 설정 시 최소 권한 원칙(least privilege)을 따르세요

---

## 의존성

- `@modelcontextprotocol/sdk` - MCP 프로토콜 구현  
- `axios` - API 요청을 위한 HTTP 클라이언트

---

## 라이선스

MIT

--- 
