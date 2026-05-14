# 대한항공 마일리지 승급 스캐너

인천(ICN) 출발 대한항공 일반석에서 프레스티지석으로 구매와 동시 승급 가능한 날짜를 수집해 보여주는 정적 웹 대시보드입니다.

라이브 사이트: https://overflow4414.github.io/mileage-web-pages/
Pages 레포: https://github.com/overflow4414/mileage-web-pages

## 지원 범위

- 승급 유형: 일반석 -> 프레스티지석
- 기본 노선: CDG, FRA, SYD, SFO, LAS
- 기본 스캔 범위: 오늘부터 45일
- 데이터 파일: `data.json`

대한항공 승급 검색은 스카이패스 로그인이 살아 있어야 합니다. 스캐너는 예매 UI 결과 대신 대한항공 승급 좌석 API를 호출합니다. 세션이 만료되면 아래 명령으로 한 번 로그인 세션을 갱신해야 합니다.

```bash
cd /Users/eunsungjo/clawd/projects/web-automation
uv run ke-login
```

브라우저에서 로그인한 뒤 터미널에서 Enter를 누르면 이후 PM2 스캐너가 저장된 세션을 사용합니다.

## 수동 실행

```bash
cd /Users/eunsungjo/clawd/projects/mileage-web
./deploy_upgrade.sh
```

환경변수로 스캔 범위를 바꿀 수 있습니다.

```bash
DAYS=90 ROUTES=ICN-CDG,ICN-FRA,ICN-SYD ./deploy_upgrade.sh
```

## PM2 실행

```bash
cd /Users/eunsungjo/clawd/projects/mileage-web
pm2 start ecosystem.config.cjs
pm2 save
```

기본 설정은 매일 한국시간 오전 9시에 실행합니다. PM2 기본값은 저장된 로그인 세션을 붙인 브라우저 컨텍스트에서 승급 좌석 API를 호출합니다. 로그는 `/tmp/ke-mileage-upgrade-deploy.log`에 남습니다.

현재 상태 확인:

```bash
pm2 list
pm2 logs ke-mileage-upgrade-scan
```

## 로컬 미리보기

```bash
cd /Users/eunsungjo/clawd/projects/mileage-web
python3 -m http.server 8000
```

브라우저에서 `http://localhost:8000`을 열면 됩니다.

## 데이터 형식

```json
{
  "updatedAt": "2026-05-12 09:00:00 KST",
  "routes": {
    "ICN-CDG": {
      "2026-06-13": ["economy_to_prestige"]
    }
  }
}
```

승급 가능일이 없는 날도 정상 데이터입니다. 이 경우 `routes`는 빈 객체로 업데이트되고, 웹 대시보드는 빈 상태를 표시합니다.
