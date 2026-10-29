# 카탄 AI Worker 설정

사이트의 AI 채팅은 Cloudflare Workers AI를 호출합니다. 한국어 규칙 설명의 품질을 높이기 위해 다국어 추론을 지원하는 `@cf/qwen/qwen3-30b-a3b-fp8` 모델을 사용하고, 답변은 최대 1,400 토큰까지 생성합니다. 주사위 합 7처럼 가이드에 정해진 주요 규칙은 정확한 안내문을 반환해 사실 오류와 불필요한 모델 사용을 줄입니다. 모델 연결은 Worker 안에서 처리하므로 브라우저에 API 키를 넣지 않습니다.

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
- Workers AI는 계정 전체에서 매일 10,000 Neurons를 무료로 제공합니다. 무료 Workers 플랜에서는 한도를 넘으면 요청이 실패하며, Workers Paid 플랜에서는 무료 할당량을 초과한 사용량에 요금이 부과될 수 있습니다. 이 AI 모델의 사용량과 한도는 계정의 다른 Workers AI 사용과 공유됩니다. 요금이 발생하지 않게 하려면 Workers Free 플랜을 유지하고 Cloudflare 대시보드에서 계정 전체 AI 사용량을 확인하세요.
- IP 주소별 요청 제한은 분당 8회로 설정되어 있습니다. Cloudflare의 위치별 제한은 근사치일 수 있습니다.
- 로컬에서 테스트할 때는 사이트를 `http://localhost:8788`로 열어야 합니다. `file://` 주소는 Worker의 허용 출처가 아닙니다.
