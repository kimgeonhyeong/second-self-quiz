# Second Self · 5분 커널 퀴즈

사람을 5문항으로 물어 **판단 방식(사고 커널)** 초안을 뽑는 정적 웹 퀴즈.
선택(A/B)만이 아니라 **"왜" 한 줄**을 받아 개인 고유의 판단 로직을 잡는다 (MBTI식 유형분류 아님).

- **라이브**: https://kimgeonhyeong.github.io/second-self-quiz/
- **결과**: 답변 → 커널 스키마 JSON 초안 자동 생성 (브라우저에서 복사/다운로드)
- 외부 의존성·서버 없음. `index.html` 하나로 동작.

---

## 응답 수집 켜기 (선택)

`index.html` 상단 `COLLECT` 한 곳만 채우면 제출→중앙 저장이 켜진다.
개인 판단 데이터라 **동의 체크박스(필수) + 삭제요청용 이메일(선택)** 이 제출 화면에 함께 뜬다.

### A) Formspree (가장 쉬움, 기본값)
1. [formspree.io](https://formspree.io) 가입 → **New form** 생성.
2. 엔드포인트 `https://formspree.io/f/XXXXXXXX` 의 `XXXXXXXX` 복사.
3. `index.html`:
   ```js
   const COLLECT = { mode:"formspree", formspreeId:"XXXXXXXX", ... };
   ```
4. 제출은 Formspree 대시보드에 쌓이고 CSV로 내보낸다. (무료 월 50건)

### B) Supabase (구조화 저장·쿼리)
1. 프로젝트 생성 → 테이블:
   ```sql
   create table kernels (
     id uuid primary key default gen_random_uuid(),
     created_at timestamptz default now(),
     deletion_token text,
     kernel jsonb
   );
   alter table kernels enable row level security;
   create policy "anon insert" on kernels for insert to anon with check (true);
   ```
   (익명 insert만 허용, 읽기는 막아 둔다.)
2. `index.html`:
   ```js
   const COLLECT = { mode:"supabase",
     supabaseUrl:"https://xxxx.supabase.co",
     supabaseKey:"<anon key>", table:"kernels" };
   ```

### C) 웹훅 (예: Google Apps Script → 시트)
`mode:"webhook", webhookUrl:"<POST 받는 URL>"`. JSON 그대로 POST.

### 끄기
`mode:"off"` → 제출 UI 숨김, 각자 JSON 다운로드만.

> 수집을 켜면 개인정보(판단 프로파일)를 다루게 된다. 처리목적 고지·삭제요청 경로를 반드시 운영한다.

MIT License.
