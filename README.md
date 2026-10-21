# lib_copilot 데모 — 사서·AI 협업 청구기호 부여 (정적 배포판)

2026 서강대학교 생성형 AI 아이디어 공모전 **우수상** 수상작 *sogang renAIssance* 팀의 데모 화면입니다.
대학도서관 편목(cataloging) 업무 화면을 재현하고, 신착도서에 청구기호(852 ▼h)를 부여할 때
AI가 후보와 근거를 제시하는 흐름을 보여줍니다.

원본 저장소: https://github.com/HAICoLab/sogang_renAIssance (`library-cataloging-demo/`, `lib_copilot/`)

## 이 저장소가 원본과 다른 점

원본 데모는 로컬 Python 서버(`library_biblio_server.py`)가 1.3GB 장서 DB를 조회하고 LLM을 실시간 호출했습니다.
이 저장소는 **서버 없이 GitHub Pages에서 그대로 돌아가도록** 데이터 공급 방식만 바꾼 버전입니다. 화면·동작 로직은 원본 그대로입니다.

| 원본 | 정적판 |
|---|---|
| `/api/biblio` → SQLite 서버 조회 | `data/books.db.gz`(장서 DB 부분집합 14만 건)를 브라우저 안 SQLite([sql.js](https://sql.js.org))로 열어 **같은 SQL** 실행 |
| `/api/classify` → lib_copilot 파이프라인 실시간 LLM 호출 | 파이프라인을 한 번 실행해 얼려둔 `data/classify/<case>.json` (수정 없음) |
| 종합목록 그리드 비어 있음 | `data/catalog_sample.json` — 원본에서 검색되던 3권 + 골든셋 중 편목 대기열이 아닌 7권(KERIS 실제 대학별 소장 기록)으로 첫 화면을 채우고, 7권도 검색되게 함. "결과 없음" 시나리오는 그대로 |

방문자가 어떤 버튼을 눌러도 외부 API는 호출되지 않습니다.


## 화면 구성

- **서지** (시작 화면) — 서강대 장서 DB를 서명·저자·청구기호·키워드·ISBN·제어번호로 검색.
- **종합목록** — KERIS 종합목록. 탭이나 메뉴를 누르면 원래 업무 화면처럼 KERIS 검색창이 뜹니다. 그리드의 책을 클릭하면 대학별 소장·분류기호가 나오고, 검색창에서도 같은 책을 찾을 수 있습니다. "부처의 생각하지 않는 연습"은 의도적으로 "결과 없음"(신착도서가 아직 어디에도 없는 시나리오).
- **편목** — 청구기호가 없는 신착도서 대기열. 각 건을 열고 **모델 실행**을 누르면 AI 추천 후보(신뢰도·근거·인용도서)와 상세 근거 화면을 볼 수 있습니다.
- **기각사례** — AI 추천이 사서의 최종 판단과 달랐던 사례 모음.

## 로컬에서 보기

```
python3 -m http.server 8000
# http://localhost:8000/library_system.html
```

(`file://`로 직접 열면 데이터 파일을 못 읽습니다. 간단한 HTTP 서버가 필요합니다.)

## 파일

```
library_system.html        화면 (원본 + 정적 데이터 계층 한 블록)
data/books.db.gz           장서 DB 부분집합 — 데모 후보 번호대 전체 + 랜덤 표본, 서지 기본 필드만
data/catalog_sample.json   종합목록 7권 + 대학별 소장 기록
data/classify/*.json       편목 대기열 5건의 AI 추천 결과 (lib_copilot 파이프라인 출력 원본)
vendor/sql-wasm.*          sql.js 1.12.0
```
