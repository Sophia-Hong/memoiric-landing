# Deploy — memoiric.com/pet

라이브 서빙 경로는 **프로덕션 서버 정적 디렉토리**다. GitHub Pages(https://sophia-hong.github.io/memoiric-landing/)는 프리뷰 미러.

## 라이브 반영 방법

1. 이 레포에서 `index.html` / `img/` 수정 후 커밋·푸시 (git이 정본).
2. Cloudways 라이브 앱 SFTP로 업로드 (자격증명 = `~/.config/memoiric/infra-secrets.md` §Cloudways 라이브 앱 SFTP):
   - 원격 경로: `public_html/pet/` (index.html + img/*.webp)
   - 도구: `sftp` + expect 래퍼 (비밀번호 인증, lftp/sshpass 없음)
3. 검증: `curl -sL -w '%{num_redirects} %{http_code} %{time_total}s' https://www.memoiric.com/pet` → 1 redirect·200·<1s

## .htaccess 이력 (2026-07-29)

- 기존: `RewriteRule ^pet/?$ <PDP URL + utm> [R=302,L]` (WP 앞단 302, IG bio 트래픽을 느린 PDP로 직행시키던 룰)
- 변경: 해당 룰 삭제 → 물리 디렉토리 `public_html/pet/` 서빙. `^pet$ → https://www.memoiric.com/pet/ [R=301]` 슬래시 정규화 룰만 추가 (http 경유 이중 리다이렉트 방지).
- 백업: 서버 `public_html/.htaccess.bak-20260729` (변경 전 원본). 원복 = rename.
- `/yt` `/pin` 숏링크 룰은 그대로 (PDP 302 유지).

## 원칙

- 인스타 bio 링크 = `memoiric.com/pet` 그대로. 링크 교체 불필요.
- 페이지 무게 예산: HTML < 10KB, 이미지 합 < 160KB, JS 0. 첫 페인트 경로 = HTML + hero만.
- 카피/후기 수정 시: 후기는 PDP 리뷰 verbatim만 (자작 금지), 가격은 WC API 라이브 실측, voice.md 린트 (em-dash 0 등).
- 랜딩 자체엔 GA4 없음 (무게 예산). 측정은 CTA의 `utm_campaign=pet_landing` → PDP GA4로.

## 2026-07-29 오후 갱신

- 히어로 = 공감 오프닝 (소현 카피: "사랑하는 가족을 잃었나요? 수많은 순간들이, 기억 속에 살아있기를" 번역).
- 가격 = 디지털 강조 박스(42% off) + 전 셀 정가 취소선 (WC variations regular/sale 실측 2026-07-29). 가격 변경 시 이 표도 갱신 필수.
- `/yt` 변형 (`yt/index.html`): CTA utm_source=youtube&utm_medium=organic_shorts. 이미지는 `/pet/img/` 절대경로 공유 (GH Pages 프리뷰에선 yt 이미지 깨짐 — 라이브 전용). .htaccess `^yt$ → /yt/ 301`.
- `/pin`은 아직 PDP 302 유지.
