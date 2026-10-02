# zidududu.github.io

한국어로 대화하는 간단한 AI 챗봇 데모입니다. (Node.js + Express)

## 동작 방식

```
브라우저(index.html)
   │  GET /translate?q=<한국어 입력>
   ▼
server.js (Express, :3000)
   1. Papago: 한국어 → 영어
   2. OpenAI Completion (text-davinci-002): 영어 응답 생성
   3. Papago: 영어 → 한국어
   ▼
브라우저: 말풍선으로 출력
```

영어 모델 성능을 활용하기 위해 입력/출력을 Papago로 번역하는 구조입니다.

## 파일 구성

| 파일 | 설명 |
|---|---|
| `index.html` | 채팅 UI. `axios`로 `http://localhost:3000/translate`를 호출하고 결과를 말풍선으로 추가 |
| `server.js` | Express 서버. `/`(index.html 제공), `/translate`(번역 + AI 응답) |
| `pakage.json` | 의존성/스크립트 정의 (파일명 오타 주의, 아래 참고) |
| `package-lock.json` | 의존성 버전 잠금 |

## 의존성

`express`, `openai@^3.1.0`, `request`, `cors`
(`cors`는 현재 `server.js`에서 사용하지 않음)

## 실행 방법

1. API 키 준비
   - OpenAI API 키
   - Naver Papago(NMT) `Client ID` / `Client Secret`
2. `server.js` 상단의 빈 값에 키 입력
   - `apiKey` (OpenAI)
   - `client_id`, `client_secret` (Papago)
   - 키를 커밋하지 마세요. 환경변수 사용을 권장합니다.
3. 의존성 설치 및 실행

```bash
npm install
node server.js
```

4. 브라우저에서 http://localhost:3000/ 접속

## API

### `GET /translate?q=<text>`

- 입력: 한국어 문장 `q`
- 응답: Papago 응답 원문(JSON 문자열). 번역된 AI 답변은 `message.result.translatedText`에 있음
- 클라이언트는 `JSON.parse(r.data).message.result.translatedText`로 파싱

## 알려진 이슈

- `pakage.json`은 `package.json`의 오타입니다. 이 이름으로는 `npm install`, `npm start`가 동작하지 않습니다. 현재는 `package-lock.json`만 인식됩니다.
- `text-davinci-002`는 OpenAI에서 지원이 종료된 모델로 알려져 있어, 현재는 호출이 실패할 수 있습니다.
- 입력값이 `insertAdjacentHTML`로 그대로 삽입되어 HTML 주입(XSS) 위험이 있습니다.
- 쿼리스트링에 입력값을 인코딩 없이 붙입니다 (`encodeURIComponent` 미사용).
- 번역/AI 호출 실패 시 서버가 응답하지 않을 수 있습니다 (일부 오류 경로에 `res` 처리 없음).
- GitHub Pages는 정적 호스팅이라 `server.js`가 실행되지 않습니다. 채팅은 로컬 서버에서만 동작합니다.
