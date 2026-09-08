# nostra-data

로또 6/45 · 연금복권 720+ 회차 데이터의 **정적 JSON 엔드포인트**.

- 상태 페이지 — https://software-lucas.github.io/nostra-data/
- `snapshot.json` — 로또 400회차 · 연금 200회차 + 알고리즘별 집계
- `stores.json` — 1·2등 배출점 (아직 수집하지 않아 빈 배열)

앱은 동행복권을 직접 부르지 않고 이 파일만 읽는다. 비공식 API 라 단말이 직접
때리면 IP 차단과 스펙 변경에 그대로 노출되기 때문이다. 스펙이 바뀌어도
수집 쪽만 고치면 되고 앱 재심사가 필요 없다.

## 이 레포는 손으로 고치지 않는다

내용은 [`software-lucas/nostra`](https://github.com/software-lucas/nostra) 의
`shared/scripts/build_cdn.py` 가 생성하고 `shared/scripts/publish_data.sh` 가 밀어 넣는다.
직접 커밋하면 다음 갱신에 덮인다.

```
동행복권 (비공식 API)          해외 IP 차단
  ↓  로컬 launchd · 주 2회      로또 토 22:30 · 연금 목 21:30 KST
  ↓  git push                   이 레포
  ↓  GitHub Pages               정적 JSON
  ↓  앱                          읽기만
```

## 왜 CI 가 아니라 로컬인가

GitHub Actions 에서 돌렸으나 러너 IP 에서 동행복권 접속이 **TCP 연결 단계에서
타임아웃**한다. 응답을 거부하는 것이 아니라 패킷이 버려진다 — 같은 코드가
국내 IP 에서는 11초에 완료된다. 그래서 수집만 국내(로컬)로 옮기고, 배포는
`git push` + GitHub Pages 로 처리한다. Cloudflare 계정도 도메인도 필요 없다.

`nostra` 레포의 CI 는 대신 **이 데이터가 낡았는지**를 검사한다
(`.github/workflows/data-freshness.yml`).

## 조건부 GET

GitHub Pages 가 `ETag` 를 주므로 `If-None-Match` 로 다시 요청하면 변경이 없을 때
`304` 가 떨어진다. 앱은 바뀐 게 없으면 본문을 내려받지 않는다.

## 면책

로또·연금 번호는 참고용이며 당첨을 보장하지 않는다. 추첨은 매 회 독립이고,
`nostra` 의 검정 결과는 알고리즘 49종 전부 우연과 구분되지 않는다는 것이다.
동행복권의 상표·서비스와 무관하다.
