# 부산 봉사 온나 · ON NOW

설치·패키지 다운로드·빌드 없이 GitHub Pages에서 실행되는 정적 프로젝트입니다. Firebase에는 공고 데이터만 저장하며 Firebase Storage는 사용하지 않습니다.

## 파일 구성

```text
index.html              화면 구성
style.css               반응형 디자인
script.js               검색·캐시·Firebase REST 연결 및 설정
assets/
  busan-bridge.svg       약 0.5KB의 다리·바다 그림
database.rules.json     Firebase Realtime Database 보안 규칙
README.md               이 안내문
.nojekyll               GitHub Pages 정적 파일 게시용 빈 파일
```

## 1. Firebase 주소 설정

Firebase 콘솔에서 프로젝트를 선택하고 Realtime Database를 생성하거나 기존 DB를 엽니다. Firestore가 아니라 Realtime Database를 사용하세요. 표시된 데이터베이스 루트 HTTPS 주소를 복사합니다.

script.js 맨 위 CONFIG.databaseURL의 빈 따옴표 안에 본인의 실제 URL을 넣으세요. CONFIG.siteURL에는 본인의 GitHub Pages 또는 Vercel 주소를 넣을 수 있습니다. 게시 주소는 안내용이며 데이터 조회에 사용하지 않습니다.

연결 주소 예시:

```js
const CONFIG = {
  databaseURL: 'https://YOUR-PROJECT-default-rtdb.asia-southeast1.firebasedatabase.app',
  siteURL: 'https://YOUR-USERNAME.github.io/YOUR-REPOSITORY/',
  pageSize: 50,
  cacheHours: 6
};
```

예시를 그대로 사용하지 말고 콘솔에 표시된 본인 주소를 사용하세요. 웹 앱의 Firebase apiKey나 SDK 설정은 필요 없습니다. 익명 공개 조회를 사용하는 REST 방식입니다. 관리자 키·공공데이터 serviceKey를 공개 파일에 넣지 마세요. 화면의 연결 설정으로 입력한 주소는 그 브라우저에만 저장되므로 모든 방문자에게 적용하려면 script.js를 수정해야 합니다.

## 2. Firebase 규칙 적용

Firebase 콘솔 → Realtime Database → 규칙에서 database.rules.json 전체를 붙여넣고 게시합니다. 파일을 GitHub에 올리는 것만으로 Firebase 규칙이 적용되지는 않습니다. 화면 하단에서도 동일 규칙을 복사할 수 있습니다.

루트 전체 읽기와 모든 클라이언트 쓰기는 차단하고, list는 키 정렬·최대 51개의 쿼리만 허용합니다. details는 개별 공고만 읽을 수 있습니다. meta는 공개 상태 정보입니다. 공개 조회용 구조이므로 개인정보를 저장하지 마세요.

## 3. 실제 공고 저장

실제 주소·계정·API 키·기존 공고 데이터는 제공되지 않았습니다. Firebase를 생성하거나 데이터를 수집·업로드한 상태가 아닙니다. Firebase 콘솔 또는 서버의 Admin SDK로 실제 자료를 넣어야 검색 결과가 나옵니다. 가짜 공고나 테스트 자료는 포함하지 않았습니다. 화면 하단의 저장 구조를 따라 list / details / meta를 만드세요. 기존 volunteers 구조는 변환이 필요합니다.

지역번호 순서는 01 중구, 02 서구, 03 동구, 04 영도구, 05 부산진구, 06 동래구, 07 남구, 08 북구, 09 해운대구, 10 사하구, 11 금정구, 12 강서구, 13 연제구, 14 수영구, 15 사상구, 16 기장군입니다.

목록 키: 지역번호_모집마감일YYYYMMDD_출처_공고번호. 같은 키를 details에도 사용합니다. 공고번호에는 영문·숫자·밑줄·하이픈만 사용하세요.

| 경로 | 필드 | 내용 |
|---|---|---|
| list/공고키 | t, r, o, s | 제목, 정확한 구·군명, 기관, 출처(1365 또는 vms) |
| list/공고키 | a, b | 활동 시작일·종료일(YYYY-MM-DD) |
| list/공고키 | rs, e | 모집 시작일·마감일(YYYY-MM-DD) |
| list/공고키 | st | 원본 모집 중 확인 시 open |
| list/공고키 | x, u | 검색용 짧은 내용·분야, 실제 확인 시각(ISO 8601) |
| details/공고키 | description, location, time | 상세 설명, 장소, 시간 |
| details/공고키 | capacity, applied, target, conditions | 모집인원, 신청인원, 대상, 조건 |
| details/공고키 | sourceUrl | 확인된 HTTPS 개별 원본 공고 주소 |
| meta | updatedAt, sources | 마지막 정상 수집 시각과 출처별 상태 |

누락 정보는 생략하세요. 모집기간과 모집 중 상태가 확인되는 공고만 기본 검색에 나옵니다.

## 4. GitHub Pages 업로드

1. ZIP을 풀고, **index.html이 있는 위치의 파일·폴더 전부**를 GitHub 저장소의 최상위에 올립니다. index.html, style.css, script.js, assets 폴더, database.rules.json, README.md, .nojekyll을 같은 구조로 유지하세요. ZIP 파일 자체나 바깥 프로젝트 폴더 한 겹을 올리지 마세요.
2. GitHub 웹 업로드를 사용하면 Add file → Upload files에서 파일과 assets 폴더를 함께 올리고 Commit changes를 누릅니다. assets/busan-bridge.svg가 폴더 안에 있는지 확인하세요. .nojekyll은 내용이 없는 보조 파일이며 업로드가 누락되어도 이 프로젝트에는 Jekyll 전용 구문이나 밑줄 경로가 없어 실행에 필수는 아닙니다.
3. 저장소 Settings → Pages → Build and deployment → Source에서 Deploy from a branch를 선택합니다.
4. Branch를 main, 폴더를 /(root)로 선택하고 Save를 누릅니다. 브랜치 이름이 다르면 업로드한 브랜치를 선택하세요.
5. Pages에 게시 완료 주소가 나타나면 접속합니다. 일반적인 프로젝트 주소는 https://내계정.github.io/저장소이름/ 입니다. GitHub Free에서는 공개 저장소를 사용하면 됩니다.

**정상 구조: 저장소 최상위에 index.html, style.css, script.js가 있고 assets 폴더 안에 SVG가 있어야 합니다.** 설치 명령, npm, Node.js, 사용자 빌드 명령은 필요 없습니다. CSS·JS·그림은 상대 경로이므로 저장소 이름이 달라져도 작동합니다.

## 5. 실행 확인과 문제 해결

- 연결 설정에서 Firebase 주소를 확인하고, 남구 또는 실제 자료가 있는 지역을 선택해 검색하세요. Enter와 검색 버튼 모두 동작합니다.
- 연결 주소 없음: script.js의 CONFIG.databaseURL을 확인하세요.
- 권한 거부: Firebase 콘솔에 규칙을 게시했는지 확인하세요. App Check 강제 적용 프로젝트는 이 SDK 없는 REST 구현과 별도 연동이 필요할 수 있습니다.
- 검색 결과 없음: 실제 list 자료, 지역번호·날짜·st 값, 모집기간을 확인하세요. 아직 읽지 않은 공고는 다음 50개 확인 버튼으로 가져옵니다.
- 디자인이나 동작 누락: 파일·폴더 이름의 대소문자 및 업로드 위치를 확인하세요.
- 이전 내용이 표시됨: 화면의 공고 캐시 지우기를 누른 뒤 검색하세요. 코드 변경 후에는 브라우저 새로고침도 하세요.

## 절약 방식

- Firebase에 웹 프로그램을 저장하지 않습니다. HTML은 GitHub Pages에, 공고만 Realtime Database에 저장합니다. Firebase Storage는 사용하지 않습니다.
- 첫 방문은 Firebase 조회 0회입니다. 입력할 때마다 요청하지 않고 검색 버튼 또는 Enter에서 조회합니다.
- 목록은 한 번에 최대 50개 표시합니다. 페이지 경계 확인을 위해 최대 51개를 요청하며 다음 페이지에서는 경계 1개를 중복 수신할 수 있습니다.
- 지역 키의 접두 범위로 서버에서 지역을 제한합니다. 키는 01~16 지역번호_모집마감일YYYYMMDD_1365또는vms_원본공고번호입니다. 예: 남구의 키는 07_로 시작합니다. 정렬을 위해 숫자 부분의 자릿수와 날짜 형식을 통일하세요.
- summary 목록에는 t,r,o,s,a,b,rs,e,st,x,u만 저장합니다. details에는 긴 설명, 장소, 시간, 인원, 조건, 확인된 개별 원본 링크만 저장합니다. x는 설명에서 뽑은 짧은 요약과 분야이며 권장 160자 이내입니다. 모집 중 확인 값은 st: open입니다. 모집기간이 없거나 불명확하면 기본 검색에서 제외합니다.
- 목록과 상세 응답은 브라우저에 6시간 보관하고 재사용합니다. 브라우저 저장이 제한되면 메모리 캐시만 사용합니다. 신선도가 더 중요하면 CONFIG.cacheHours를 줄이세요. 캐시는 이 브라우저에서만 재사용됩니다.
- 전체 DB 읽기, 상시 구독, 자동 폴링, 사진·폰트 다운로드, 검색 로그 저장을 하지 않습니다. 키는 기본 인덱스를 이용하므로 별도 .indexOn이 필요하지 않습니다.
- 별도 수집 서버에서는 변경된 필드만 갱신하고, 성공적으로 확인된 종료 공고는 list와 details에서 함께 삭제하세요. 마감일 변경으로 키가 바뀌면 이전 키를 삭제하고 새 키를 원자적으로 반영합니다. 수집 실패 시 기존 정상 자료를 지우지 않습니다. 개인정보와 중복 원문·이미지·누적 로그는 저장하지 마세요.

## 검색 범위와 연결 한계

공고를 추가로 읽으려면 '다음 50개 확인'을 눌러야 합니다. 읽지 않은 페이지는 검색 결과에 포함되지 않으며 화면에 전체 건수로 표시하지 않습니다. 결과는 현재 읽은 공고 중 모집 마감일순입니다. 제목·기관·검색용 요약·지역을 검색하며, 아직 가져오지 않은 긴 설명 전체를 검색하지 않습니다. 모든 공고의 즉시 전체 검색에는 별도의 검색 서버가 필요합니다.

GitHub Pages의 정적 웹사이트에서는 비밀 API 키를 안전하게 숨기거나 사이트가 닫힌 동안 예약 수집을 수행할 수 없습니다. 1365 API 실제 연동·XML 변환·하루 1회 수집은 별도 서버 작업이 필요하며 이 정적 프로젝트에 구현되어 있지 않습니다. VMS도 허가된 공식 수단을 확보한 경우에만 연결해야 합니다. script.js 등 공개 파일에 API 키나 관리자 인증정보를 넣지 마세요. Firebase 콘솔/서버가 데이터를 넣으면 이 앱은 실제 저장 자료를 조회합니다. API 인증 및 데이터 저장·배포는 수행하거나 검증하지 않았습니다.

meta는 정상 수집 후 updatedAt(ISO 8601), sources의 1365와 vms 상태(connected 또는 unavailable)를 기록합니다. 아직 연결하지 않은 출처를 connected로 기록하지 마세요. 공고별 u도 실제 확인 시각으로 기록합니다.

규칙은 공용 목록의 제한된 조회와 개별 상세 읽기만 허용합니다. 모든 브라우저 쓰기를 막지만, 공개 데이터의 반복 조회 횟수나 월간 청구 금액까지 제한하지는 못합니다. Admin SDK는 보안 규칙을 우회하므로 서버의 자격증명을 보호해야 합니다.

## 확인한 내용

JavaScript 문법, 초기 조회 없음, 지역 구분(동구/동래구), 복합 검색, 띄어쓰기, 모집 완료 제외, 날짜 검증, 원본 링크 검증, 50개 페이지와 중복 제거, 캐시 재사용, 권한 거부 처리를 모의 데이터로 확인했습니다. 테스트용 가짜 공고는 배포 파일에 포함하지 않았습니다. 실제 Firebase·1365·VMS 연결 및 브라우저의 시각적 검증은 수행하지 않았습니다.

## 공식 문서

- [Firebase REST 조회](https://firebase.google.com/docs/database/rest/retrieve-data)
- [쿼리 조건을 이용한 보안 규칙](https://firebase.google.com/docs/database/security/rules-conditions)
- [1365 공식 API](https://www.data.go.kr/data/15157582/openapi.do)

- [GitHub Pages 게시 소스 설정](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
