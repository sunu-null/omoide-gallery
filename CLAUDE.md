# Omoide Gallery

나만 사용하는 사진 기반 추억 기록 앱. 서버 비용 없이 로컬 자원만 사용한다.

## 문서 언어
- CLAUDE.md, 계획 문서: 한국어

## 사용 환경
- 클라이언트: iPhone 15 Pro (Safari, 홈 화면에 추가한 PWA)
- 서버: 개인 Mac
- 접속: Tailscale (외출 중에도 Mac에 접속)
  - PWA(Service Worker)는 HTTPS가 필요하므로 `tailscale serve`로 HTTPS를 제공한다
- 사용자: 본인 1명. 외부 API 없음

## 구조
```
iPhone (PWA) ──Tailscale──▶ Mac: Go 서버 ── SQLite (메타데이터·추억 기록)
                                       └─ 로컬 폴더 (사진 원본)
```

## 기술 스택
- 프론트엔드: Vite + React + TypeScript (strict), PWA
- 백엔드: Go
- DB: SQLite
- 이미지 저장소: Mac 로컬 디스크 (S3 사용 안 함)
- 백업: Time Machine
- 패키지 매니저: bun

## 디렉터리 (초안)
```
omoide-gallery/
├─ web/      # Vite + React + TS
├─ server/   # Go API
└─ data/     # git 제외: photos/, omoide.db
```

## 브랜치
- `main`: 안정 버전
- `develop`: 개발 통합 브랜치. 작업 브랜치는 여기서 분기하고 여기로 머지한다
- PR 라벨은 브랜치 이름으로 자동 부착 (`.github/labeler.yml`): `feat/`→`feature`, `fix/`→`bug`, `chore/`→`chore`, `docs/`→`docs`, `develop`→`main`은 `deploy`
- 작업 브랜치: `feat/{간략한 기능 설명}`, `fix/{간략한 버그 설명}`, `chore/{간단한 설명}` 등 (kebab-case)

## CI
- GitHub Actions (`.github/workflows/`), `main`·`develop` 대상 push/PR에서 실행
- `server.yml`: `server/**` 변경 시 `go vet`, `go test`
- `web.yml`: `web/**` 변경 시 `bun install`, `bun run build` (타입 체크 포함)

## 규칙
- 테스트: Go는 `go test`, 프론트는 로직이 생길 때만 vitest
- 사진 EXIF(촬영일, 위치)를 추억 기록에 활용한다
- iPhone의 HEIC 사진은 업로드할 때 Safari가 JPEG로 변환한다
- 디자인 파일 없이 브라우저에서 확인하며 개발한다
- 그 외 코딩 스타일, 커밋, 브랜치 규칙은 전역 규칙을 따른다
