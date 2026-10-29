# 카탄 AI Worker 설정

사이트의 AI 채팅은 Cloudflare Workers AI를 호출합니다. 모델 연결은 Worker 안에서 처리하므로 브라우저에 API 키를 넣지 않습니다.

## Worker 배포

1. Cloudflare 계정에서 Workers AI를 활성화하고 사용량/요금 정책을 확인합니다.
2. Node.js가 설치된 터미널에서 저장소 루트로 이동합니다.
3. 다음 명령을 실행하고 Cloudflare 계정으로 로그인한 뒤 Worker를 배포합니다.

```sh
npx wrangler login
npx wrangler deploy --config ai-worker/wrangler.jsonc
```

배포가 끝나면 `https://catan-guide-ai.<계정-subdomain>.workers.dev` 형식의 주소가 표시됩니다.

## 사이트에 Worker 연결

`index.html`의 다음 메타 태그에 배포된 Worker 주소를 넣습니다. `/api/chat` 경로는 붙이지 않습니다.

```html
<meta name="catan-ai-endpoint" content="https://catan-guide-ai.<계정-subdomain>.workers.dev">
```

수정한 `index.html`을 새 사이트 저장소의 `main` 브랜치에 배포하면 AI 버튼에서 질문할 수 있습니다. Worker는 현재 `https://is2530.github.io` 사이트 요청만 허용합니다.

## 참고

- 채팅 대화는 이 사이트 코드에서 저장하지 않고, 현재 페이지가 열린 동안에만 이어집니다. 질문과 대화 내용은 답변 생성을 위해 Cloudflare Workers AI로 전송됩니다.
- Workers AI 사용량에 따라 Cloudflare 요금이 발생할 수 있습니다. 실제 사용 전 계정의 최신 요금 및 개인정보 처리 조건을 확인하세요.
- IP 주소별 요청 제한은 분당 8회로 설정되어 있습니다. Cloudflare의 위치별 제한은 근사치일 수 있습니다.
- 로컬에서 테스트할 때는 사이트를 `http://localhost:8788`로 열어야 합니다. `file://` 주소는 Worker의 허용 출처가 아닙니다.
