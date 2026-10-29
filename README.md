# 서울 버스 도착 정보 확인

서울 버스 API를 이용해 버스 도착 예정 시간을 확인하고, 정류장까지의 도보 시간을 고려해 탑승 여유 시간을 계산하는 웹 애플리케이션입니다.

## 주요 기능

- **실시간 버스 도착 정보 조회**: 서울 버스 API를 통해 버스 도착 예정 시간을 확인합니다.
- **도착 시간 표시**: API에서 받은 도착 예정 시간을 분 단위로 변환해 표시합니다.
- **도보 시간 비교**: 사용자가 설정한 도보 시간과 버스 도착 예정 시간을 비교합니다.
- **탑승 여유 시간 계산**: 도착 예정 시간에서 도보 시간을 빼서 남은 시간을 계산합니다.
- **탑승 상태 안내**: 남은 시간에 따라 탑승 여유, 서둘러 이동, 시간 촉박 상태를 표시합니다.
- **오류 처리**: API 요청 실패 및 응답 오류 발생 시 상태 메시지를 표시합니다.

## 기술 스택

| 구분 | 기술 |
|---|---|
| 프런트엔드 | HTML, CSS, JavaScript |
| 버스 정보 | 서울 버스 도착 정보 API |
| 서버 및 API 중계 | Cloudflare Workers |
| 웹 호스팅 | GitHub Pages |
| 응답 데이터 | XML |

## 서비스 구조

```text
사용자 브라우저
    │
    ▼
GitHub Pages
(index.html)
    │
    ▼
Cloudflare Workers
(API 중계 및 인증키 관리)
    │
    ▼
서울 버스 API
    │
    ▼
버스 도착 정보 XML 반환
    │
    ▼
도착 시간 계산 및 화면 표시
```

## API 연동

서울 버스 API의 도착 정보 조회 기능을 사용합니다.

- API 경로: `/api/rest/arrive/getArrInfoByRoute`
- 정류소 ID: `stId`
- 노선 ID: `busRouteId`
- 정류소 순번: `ord`

브라우저는 정류소 ID, 노선 ID, 정류소 순번을 Cloudflare Worker로 전달합니다. Worker는 서버에 저장된 인증키를 추가해 서울 버스 API를 호출하고, 응답 데이터를 브라우저에 반환합니다.

## 탑승 여유 시간 계산

- 도착 예정 시간(분) = 도착 예정 시간(초)을 60으로 나눈 후 올림
- 탑승 여유 시간(분) = 도착 예정 시간(분) - 도보 시간(분)

| 조건 | 상태 | 안내 |
|---|---|---|
| 여유 시간 3분 이상 | 탑승 여유 | 천천히 이동하셔도 괜찮아요. |
| 여유 시간 1~2분 | 서둘러 이동 | 조금 서둘러 주세요. |
| 여유 시간 0분 이하 | 시간 촉박 | 다음 버스도 확인해 보세요. |

※ 실제 탑승 가능 여부는 교통 상황, 도착 정보 갱신 시점 및 보행 속도에 따라 달라질 수 있습니다.

## 보안 및 배포

- 서울 버스 API 인증키는 Cloudflare Workers의 Secret 환경변수로 관리합니다.
- 프런트엔드에서는 Cloudflare Worker를 통해서만 버스 API를 호출합니다.
- Cloudflare Worker는 GitHub Pages의 웹사이트 출처를 기준으로 CORS 요청을 제한합니다.
- 웹페이지는 GitHub Pages를 통해 배포합니다.

### 배포 구성

- 프런트엔드: GitHub Pages
- API 중계 서버: Cloudflare Workers
- 인증키 환경변수: `SEOUL_BUS_SERVICE_KEY`

## 프로젝트 주소

- 웹페이지: https://harteh.github.io/seoul-bus-check/
- GitHub 저장소: https://github.com/harteh/seoul-bus-check
- API 서버: `https://seoul-bus-api.harteh31.workers.dev/api/bus`

## 향후 개선 사항

- 여러 정류소 및 노선 선택 기능
- 버스 도착 정보 자동 갱신
- API 요청 횟수 제한 및 오류 로그 관리
- 모바일 화면 최적화
