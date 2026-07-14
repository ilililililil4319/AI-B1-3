# AI-B1-3

 # [프로젝트 1] 자동화 도구 비교 구현 보고서

## Make vs Zapier — Google Sheets 기반 설문 알림 자동화 비교 분석



**작성일 : 2026년 7월 14일**

**비교 도구 : Make (Integromat) vs Zapier**

**자동화 주제 : 여름철 피서지 선호도 설문 자동 알림 시스템**



---



## 1. 개요



### 1-1. 보고서 목적



본 보고서는 동일한 자동화 워크플로우를 Make와 Zapier 두 가지 도구로 각각 구현하고, 그 과정과 결과를 비교 분석한 것이다. 두 도구의 UI/UX, 설정 난이도, 연동 서비스 범위, 무료 플랜 범위, 실행 로그 확인 방식 등 5개 이상의 항목을 기준으로 비교하여, 각 도구의 특성과 적합한 사용 상황을 파악하는 것을 목적으로 한다.



### 1-2. 과제 목표 반영



본 보고서를 통해 다음 내용을 스스로 설명할 수 있도록 구성하였다.



- **Trigger와 Action의 개념** : 어떤 이벤트가 발생했을 때(Trigger) 어떤 동작이 실행되는지(Action)를 두 도구에서 각각 어떻게 설정하는지 비교

- **조건 분기(Filter/Router)의 역할** : 특정 조건을 만족하는 데이터만 다음 단계로 전달하는 구조를 Make(Filter + Router)와 Zapier(Filter by Zapier)에서 각각 어떻게 구현하는지 비교

- **서로 다른 자동화 도구의 특징 비교** : 동일한 워크플로우를 두 도구로 구현하며 체감한 차이를 항목별로 정리

- **특정 업무에 적합한 도구 선택 및 이유 설명** : 비교 분석 결과를 바탕으로 상황별 추천 도구 제시

- **자동화 흐름을 단계별로 설명** : 각 도구의 구현 구조를 단계별로 서술



### 1-3. 기능 요구 사항 충족 여부



| 구분 | 요구 내용 | Make | Zapier |

|------|-----------|------|--------|

| Trigger | 1개 이상 포함 | ✅ Google Sheets – Watch New Rows | ✅ Google Sheets – New Spreadsheet Row |

| Action | 2개 이상 포함 | ✅ Slack + Gmail (Router로 병렬 연결) | ✅ Gmail + Slack (순차 연결) |

| 조건 분기 | 1개 이상, 실제 실행 결과 확인 | ✅ Filter (4점 OR 5점) + Router | ✅ Filter by Zapier (4점 OR 5점) |

| 동작 여부 | 실제 동작하는 워크플로우 | ✅ 실제 실행 및 결과 캡처 확인 | ✅ 실제 실행 및 결과 캡처 확인 |



---



## 2. 자동화 주제 및 워크플로우 구조



### 2-1. 자동화 주제



**"여름철 피서지 선호도 설문 자동 알림 시스템"**



Google Form으로 제작한 "올여름 가고 싶은 곳은 어디인가요?" 설문(7개 문항)에서, 응답자가 마지막 문항에서 선택한 **장소 선호도 점수(1~5점)가 4점 이상인 경우에만** Gmail과 Slack 두 채널로 알림이 자동 전송되도록 설계하였다.



### 2-2. 공통 워크플로우 구조



두 도구 모두 아래와 동일한 논리 구조로 구현하였다.



```

[Trigger] Google Sheets에 새 행 추가 감지

        ↓

[Filter] 선호도 점수 4점 또는 5점인 경우만 통과 (OR 조건)

        ↓

[Action 1] Gmail – 알림 이메일 발송

[Action 2] Slack – 채널 메시지 전송

```



### 2-3. 도구별 구현 구조 비교



| 구분 | Make | Zapier |

|------|------|--------|

| Trigger | Google Sheets – Watch New Rows | Google Sheets – New Spreadsheet Row |

| 조건 분기 방식 | Filter 모듈 + Router (병렬 분기) | Filter by Zapier (단일 필터, 순차 처리) |

| Action 1 | Slack – Send a Message | Gmail – Send Email |

| Action 2 | Gmail – Send an email | Slack – Send Channel Message |

| 전체 구조 | Trigger → Filter → Router → Slack / Gmail (동시) | Trigger → Filter → Gmail → Slack (순차) |



---



## 3. 도구별 구현 과정 요약



### 3-1. Make 구현 과정 (7월 13일~14일)



Make에서는 이틀에 걸쳐 구현을 진행하였다. 7월 13일에는 기본 워크플로우(Trigger – Filter – Slack)를 완성하고, 7월 14일에는 Router를 추가하여 Gmail 액션을 병렬로 연결하였다.



**주요 구현 단계**



1. Make에서 새 Scenario 생성

2. Google Sheets Trigger 연결 (Watch New Rows) — Drive 항목 누락 오류 발생 → My Drive 재선택으로 해결

3. Filter 설정 (선호도 4점 이상만 통과) — 초기 "4점" 조건만 설정하여 5점 응답 누락 → OR 조건 추가로 해결

4. Slack – Send a Message 액션 추가

5. Google Sheets – Add a Row 액션 추가

6. Limit 값 조정 (기본값 1 → 100으로 수정) — 응답 9건 중 1건만 처리되는 문제 해결

7. 트리거 커서 초기화 (Choose where to start → All 선택) — 필터 수정 후 재실행 시 0건 처리 문제 해결

8. Router 추가 → Gmail(Send an email) 액션 병렬 연결 (7월 14일)

9. 최종 테스트 : Google Sheets 10건 처리 → Filter 10건 통과 → Slack 10건, Gmail 10건 모두 정상 전송 확인



**발생한 트러블슈팅 요약**



| 순번 | 문제 상황 | 원인 | 해결 방법 |

|------|-----------|------|-----------|

| 1 | Run once 실행 시 오류 | Google Sheets Drive 항목 값 누락 | Drive를 My Drive로 재선택 후 저장 |

| 2 | 4점 이상 9건 중 1건만 처리 | Trigger의 Limit 기본값(1) 제한 | Limit 값을 100으로 상향 조정 |

| 3 | Limit 수정 후에도 3건만 통과 | Filter의 OR 조건(5점 포함) 누락 | Add OR rule로 "5점" 조건 추가 |

| 4 | 필터 수정 직후 통과 0건 | Watch New Rows 트리거 커서가 끝까지 이동 | Choose where to start → All로 커서 초기화 |

| 5 | Action이 Slack 1개뿐 (과제 요구사항 미충족) | 초기 설계 시 액션 1개만 연결 | Router 추가 후 Gmail 액션 병렬 연결 |

| 6 | Gmail 모듈 추가 직후 Connection 오류 | 구글 계정 미연동 | Create a connection으로 구글 계정 로그인 및 권한 허용 |



### 3-2. Zapier 구현 과정 (7월 14일)



Zapier에서는 하루 동안 구현을 완료하였다. Copilot 기능을 활용하여 자연어 프롬프트 한 줄로 기본 Zap 초안을 자동 생성한 뒤, 세부 설정과 Filter 추가를 통해 완성하였다.



**주요 구현 단계**



1. Zapier 가입 및 사용 앱 선택 (Gmail, Google Sheets, Slack)

2. Copilot에 자연어 프롬프트 입력 → Trigger 1개 + Action 2개(Gmail, Slack) 초안 Zap 자동 생성

3. Google Sheets / Gmail / Slack 계정 연동 (연결 만료 오류 발생 → 재연동)

4. Trigger(New Spreadsheet Row) 세부 설정 — 스프레드시트/워크시트 지정

5. Action 1 : Gmail(Send Email) 필드 설정 및 테스트

6. Action 2 : Slack(Send Channel Message) 필드 설정 및 1차 Publish

7. 처리 건수 불일치 발견 (다수 응답 중 1~2건만 처리) — 트리거 구조 파악 후 신규 응답 추가로 검증

8. Filter by Zapier 추가 (선호도 4점 OR 5점) — Trigger와 Action 사이에 배치

9. Filter 추가 후 단계 재배열 확인 및 재게시(Publish)

10. 최종 테스트 : 필터 통과 데이터만 Gmail·Slack 두 채널로 정상 전달 확인



**발생한 트러블슈팅 요약**



| 순번 | 문제 상황 | 원인 | 해결 방법 |

|------|-----------|------|-----------|

| 1 | Google Sheets 연결 오류 | 계정 연결(Connection)이 만료됨 | "Reconnect this account"로 Google 계정 재연동 |

| 2 | 다수 응답인데 1~2건만 도착 | New Spreadsheet Row 트리거는 가장 최근 행 1건만 감지 | 신규 응답 추가 후 재실행하여 정상 트리거 확인 |

| 3 | Zap history 과거 기록 조회 불가 | 실행 기록 URL 만료 등 ("Can't access this run") | Zap Runs 로그 및 Run details에서 개별 실행 데이터 직접 확인 |

| 4 | 기존 누적 응답 일괄 처리 불가 | "Transfer data" 기능이 유료(Paid) 플랜 전용 | 무료 체험판에서는 구현 어려워 신규 응답 추가 방식으로 대체 |

| 5 | 조건 분기(Filter) 미포함 | Copilot 초안 Zap에 Filter 단계 미포함 | Filter by Zapier 추가(4점 OR 5점) 후 Gmail·Slack 앞에 배치 |



---



## 4. 비교 분석



### 4-1. UI/UX (인터페이스 및 사용 경험)



| 항목 | Make | Zapier |

|------|------|--------|

| 인터페이스 방식 | 캔버스 기반 시각적 플로우 (모듈을 드래그하여 연결) | 단계별 리스트 방식 (위에서 아래로 순차 구성) |

| 초보자 진입 장벽 | 상대적으로 높음 — 모듈 연결, 커서 초기화, Router 개념 등 직접 학습 필요 | 낮음 — Copilot이 초안을 자동 생성해주어 빠르게 시작 가능 |

| 워크플로우 가시성 | 높음 — 전체 흐름이 캔버스에 한눈에 보임 | 보통 — 단계 목록 형태로 직관적이나 복잡한 분기 시 가독성 저하 |

| 모듈 추가 방식 | 캔버스에서 직접 클릭하여 모듈 추가 및 연결 | 단계 사이에 "+" 버튼으로 추가 |

| AI 지원 | 없음 (직접 설정) | Copilot — 자연어 프롬프트로 초안 자동 생성 |



**평가** : Zapier는 Copilot 덕분에 초보자가 빠르게 시작할 수 있다. Make는 초기 진입 장벽이 있지만, 캔버스 기반의 시각적 구조 덕분에 복잡한 워크플로우를 한눈에 파악하기 유리하다.

### 4-2. 설정 난이도

| 항목 | Make | Zapier |
|------|------|--------|
| 초기 설정 | 모든 모듈을 직접 추가하고 연결해야 함 | Copilot이 Trigger + Action 2개 초안을 자동 생성 |
| 계정 연동 | 모듈별로 Connection 생성 (구글 계정 OAuth 직접 진행) | 앱별 계정 연동 (만료 시 Reconnect 안내 제공) |
| 조건 분기 설정 | Filter 모듈을 별도로 추가 후 OR 조건 직접 구성, Router로 병렬 분기 추가 설정 필요 | Filter by Zapier를 단계 사이에 삽입, OR 조건(Or continue if) 설정 |
| 필드 매핑 | 모듈 내 변수 패널에서 직접 드래그 또는 클릭하여 매핑 | 드롭다운 방식으로 시트 변수 선택 — 비교적 직관적 |
| 트리거 커서 개념 | 커서(처리 기준점) 개념을 직접 이해하고 초기화해야 함 (Choose where to start → All) | 별도 커서 개념 없음 — 가장 최근 신규 행만 감지하는 구조 |
| 전체 난이도 | 중~상 — 개념 이해와 직접 설정이 많아 학습 곡선 존재 | 하~중 — Copilot 덕분에 초기 진입이 쉬우나 Filter 추가 시 단계 재배열 확인 필요 |

**평가** : Make는 설정 항목이 많고 커서 초기화, Router 개념 등 직접 학습해야 할 요소가 많아 초보자에게 다소 어렵게 느껴질 수 있다. 반면 Zapier는 Copilot이 초안을 자동 생성해주어 진입 장벽이 낮지만, Filter 추가 후 단계 번호가 밀리는 등 세부 재확인이 필요한 상황이 발생하였다.

---

### 4-3. 연동 서비스 범위

| 항목 | Make | Zapier |
|------|------|--------|
| 연동 앱 수 | 1,000개 이상 | 7,000개 이상 |
| 주요 연동 앱 | Google Workspace, Slack, Notion, Airtable, HTTP/Webhook 등 | Google Workspace, Slack, Notion, Salesforce, HubSpot 등 비즈니스 SaaS 다수 |
| Webhook 지원 | 무료 플랜에서도 지원 | 무료 플랜에서 제한적 지원 |
| 커스텀 API 연동 | HTTP 모듈로 직접 API 호출 가능 (무료 포함) | Webhooks by Zapier — 유료 플랜에서 본격 지원 |
| 본 과제 사용 앱 | Google Sheets, Slack, Gmail — 모두 무료 플랜에서 연동 가능 | Google Sheets, Slack, Gmail — 모두 무료 플랜에서 연동 가능 |

**평가** : 연동 앱 수는 Zapier가 압도적으로 많아 기업용 SaaS와의 연동에 유리하다. Make는 앱 수는 적지만 Webhook과 HTTP 모듈을 무료로 지원하여 개발자 친화적인 커스텀 연동이 가능하다. 본 과제에서 사용한 Google Sheets, Gmail, Slack은 두 도구 모두 무료 플랜에서 문제없이 연동되었다.

---

### 4-4. 무료 플랜 범위

| 항목 | Make | Zapier |
|------|------|--------|
| 무료 플랜 명칭 | Free | Free |
| 월 실행 횟수 | 1,000 Operations/월 | 100 Tasks/월 |
| Zap/Scenario 수 | 무제한 Scenario | 5개 Zap |
| 단계(Step) 수 제한 | 제한 없음 | 2단계 (Trigger + Action 1개) — Filter, 다중 Action은 유료 |
| 조건 분기(Filter) | 무료 지원 | **유료 플랜 전용** (본 과제에서는 무료 체험판으로 사용) |
| 다중 Action | 무료 지원 (Router로 병렬 연결 포함) | **유료 플랜 전용** (무료는 Action 1개만) |
| 실행 주기 | 최소 15분 간격 | 최소 15분 간격 (무료 기준) |
| Transfer data (일괄 처리) | 해당 기능 없음 (Limit 값으로 대응) | **유료 플랜 전용** |
| 본 과제 무료 구현 가능 여부 | ✅ 가능 (Filter + Router + 다중 Action 모두 무료) | ⚠️ 제한적 — Filter와 다중 Action은 무료 체험판(Trial)으로만 사용 가능, 순수 무료 플랜으로는 구현 불가 |

**평가** : Make는 Filter, Router, 다중 Action 등 본 과제의 핵심 기능을 모두 무료 플랜에서 구현할 수 있었다. 반면 Zapier는 순수 무료 플랜(Free)으로는 Filter와 다중 Action을 사용할 수 없으며, 본 과제에서는 14일 무료 체험판(Trial)을 통해 구현하였다. 장기적으로 무료 환경에서 복잡한 자동화를 구현하려면 Make가 유리하다.

---

### 4-5. 실행 로그 확인 방식

| 항목 | Make | Zapier |
|------|------|--------|
| 로그 확인 위치 | Scenario 실행 후 캔버스에서 각 모듈 클릭 → 실행 결과 팝업 확인 | Zap Runs 메뉴 → 개별 실행 클릭 → Run details에서 단계별 확인 |
| 실시간 확인 | Run once 실행 시 캔버스에서 즉시 시각적으로 확인 가능 (모듈별 처리 건수 표시) | 실행 후 Zap Runs 목록에서 확인 (약간의 지연 있음) |
| 데이터 상세 확인 | 모듈 클릭 시 Bundle 단위로 입력/출력 데이터 전체 확인 가능 | Run details에서 각 단계의 Data in / Data out 확인 가능 |
| 오류 표시 방식 | 오류 발생 모듈에 빨간 경고 아이콘 표시, 메시지 팝업 | 실행 목록에서 "Failed" 상태 표시, 단계별 오류 메시지 확인 |
| 과거 기록 접근 | Scenario History에서 과거 실행 기록 확인 가능 | Zap History — 일부 기록은 "Can't access this run" 오류로 접근 불가 (URL 만료 등) |
| 필터 통과 여부 확인 | Filter 모듈에서 통과/차단 건수 숫자로 표시 | Zap Runs에서 "Your run passed the rules for this filter." 메시지로 확인 |

**평가** : Make는 캔버스에서 실행 즉시 모듈별 처리 결과를 시각적으로 확인할 수 있어 디버깅이 직관적이다. Zapier는 Zap Runs를 통해 단계별 데이터를 상세히 확인할 수 있으나, 일부 과거 기록에 접근이 제한되는 경우가 있었다.

---

### 4-6. 조건 분기(Filter/Router) 구현 방식 비교

| 항목 | Make | Zapier |
|------|------|--------|
| 분기 모듈 이름 | Filter + Router | Filter by Zapier |
| 설정 위치 | 모듈 사이 연결선에서 우클릭 → Set up a filter / Add router | Trigger와 Action 사이에 단계로 삽입 |
| OR 조건 설정 | "Add OR rule"로 조건 추가 | "Or continue if" 규칙 추가 |
| 병렬 분기 | Router로 여러 경로 동시 처리 가능 (Slack과 Gmail에 동시 전달) | 순차 처리만 가능 (Gmail → Slack 순서로 실행) |
| 필터 통과 후 처리 | Router를 통해 두 Action이 동시에 실행됨 | Filter 통과 후 Action이 순서대로 실행됨 |
| 무료 지원 여부 | ✅ 무료 | ❌ 유료 플랜 전용 (본 과제는 Trial로 사용) |

**평가** : Make의 Router는 하나의 데이터를 여러 경로로 동시에 분기할 수 있어 병렬 처리에 강점이 있다. Zapier의 Filter by Zapier는 설정이 단순하고 직관적이지만 순차 처리만 가능하며, 무료 플랜에서는 사용할 수 없다는 제약이 있다.

---

## 5. 각 도구의 장단점 정리

### 5-1. Make

**장점**
- 캔버스 기반 시각적 인터페이스로 복잡한 워크플로우도 한눈에 파악 가능
- Filter, Router, 다중 Action 등 고급 기능을 무료 플랜에서 모두 사용 가능
- Webhook, HTTP 모듈 등 개발자 친화적 기능을 무료로 지원
- 실행 시 캔버스에서 모듈별 처리 결과를 즉시 시각적으로 확인 가능
- 월 1,000 Operations로 소규모 자동화에 충분한 무료 실행 횟수 제공

**단점**
- 초보자에게 진입 장벽이 높음 — 커서 초기화, Router 개념 등 직접 학습 필요
- AI 기반 초안 생성 기능 없음 — 모든 모듈을 직접 추가하고 설정해야 함
- 연동 앱 수가 Zapier 대비 적음 (1,000개 vs 7,000개)
- 설정 오류 발생 시 원인 파악에 시간이 걸릴 수 있음

### 5-2. Zapier

**장점**
- Copilot 기능으로 자연어 프롬프트 한 줄만으로 기본 Zap 초안 자동 생성 — 초보자 진입 장벽 매우 낮음
- 7,000개 이상의 앱 연동 지원 — 기업용 SaaS와의 연동에 강점
- 단계별 리스트 UI가 직관적이어서 설정 흐름을 따라가기 쉬움
- Zap Runs에서 단계별 Data in/out을 상세히 확인 가능

**단점**
- 무료 플랜(Free)에서는 Filter와 다중 Action 사용 불가 — 본 과제 수준의 자동화도 유료 플랜 필요
- 순차 처리만 가능 — Make의 Router처럼 병렬 분기 불가
- Transfer data(일괄 처리) 기능이 유료 전용 — 기존 누적 데이터 일괄 처리 불가 (무료 환경)
- Zap history 일부 기록 접근 제한 ("Can't access this run")
- 무료 플랜 월 100 Tasks로 실사용 시 금방 소진될 수 있음

---

## 6. 적합한 사용 상황 의견

### 6-1. Make가 적합한 상황

| 상황 | 이유 |
|------|------|
| 무료 환경에서 복잡한 자동화를 구현해야 할 때 | Filter, Router, 다중 Action 등 고급 기능을 무료로 사용 가능 |
| 병렬 분기가 필요한 워크플로우 | Router로 하나의 데이터를 여러 채널에 동시 전달 가능 |
| 개발자 또는 기술적 배경이 있는 사용자 | HTTP 모듈, Webhook 등 커스텀 연동에 강점 |
| 복잡한 워크플로우를 시각적으로 관리하고 싶을 때 | 캔버스 기반 UI로 전체 흐름을 한눈에 파악 가능 |
| 실행 결과를 즉시 디버깅해야 할 때 | Run once 후 캔버스에서 모듈별 처리 결과 즉시 확인 가능 |


---

### 6-2. Zapier가 적합한 상황

| 상황 | 이유 |
|------|------|
| 자동화를 처음 시작하는 초보자 | Copilot이 자연어 프롬프트 한 줄로 기본 Zap 초안을 자동 생성해주어 빠르게 시작 가능 |
| 다양한 기업용 SaaS 앱과 연동이 필요할 때 | 7,000개 이상의 앱을 지원하여 Salesforce, HubSpot 등 비즈니스 전용 앱 연동에 강점 |
| 단순한 선형(순차) 자동화를 빠르게 구축할 때 | 복잡한 분기 없이 Trigger → Action으로 이어지는 단순 흐름은 Zapier가 더 빠르게 설정 가능 |
| 팀 단위로 자동화를 관리하고 공유해야 할 때 | 단계별 리스트 UI가 직관적이어서 비기술 직군 팀원도 Zap 구조를 쉽게 파악 가능 |
| 유료 플랜 예산이 확보된 비즈니스 환경 | 유료 플랜에서 Filter, 다중 Action, Transfer data 등 고급 기능을 모두 활용 가능 |

---

## 7. 결론

본 보고서에서는 "여름철 피서지 선호도 설문 자동 알림 시스템"이라는 동일한 자동화 워크플로우를 Make와 Zapier 두 도구로 각각 구현하고, 6개 항목(UI/UX, 설정 난이도, 연동 서비스 범위, 무료 플랜 범위, 실행 로그 확인 방식, 조건 분기 구현 방식)에 걸쳐 비교 분석하였다.

두 도구는 지향하는 사용자층과 강점이 명확히 다르다.

**Make**는 무료 플랜에서도 Filter, Router, 다중 Action 등 고급 기능을 모두 사용할 수 있고, 캔버스 기반의 시각적 인터페이스 덕분에 복잡한 워크플로우를 직관적으로 관리할 수 있다. 다만 초보자에게는 커서 초기화, Router 개념 등 직접 학습해야 할 요소가 많아 진입 장벽이 존재한다. **비용 제약이 있거나 복잡한 분기·병렬 처리가 필요한 상황에 적합하다.**

**Zapier**는 Copilot 기능 덕분에 자연어 프롬프트 한 줄만으로 기본 워크플로우 초안을 자동 생성할 수 있어 초보자가 빠르게 시작하기에 최적화되어 있다. 또한 7,000개 이상의 앱 연동을 지원하여 다양한 비즈니스 SaaS 환경에서 강점을 발휘한다. 다만 Filter와 다중 Action이 유료 플랜 전용이라 무료 환경에서는 본 과제 수준의 자동화도 구현에 제약이 따른다. **빠른 초기 구축이 필요하거나 다양한 앱 연동이 중요한 비즈니스 환경에 적합하다.**

두 도구를 직접 사용해본 결과, 단순히 "어떤 도구가 더 좋다"고 단정하기보다는 **사용자의 기술 수준, 예산, 자동화의 복잡도, 연동이 필요한 앱의 종류**에 따라 적합한 도구가 달라진다는 것을 체감할 수 있었다. 자동화 도구를 선택할 때는 현재 상황과 목적에 맞는 도구를 비교 검토한 뒤 결정하는 것이 중요하다.

---

> **유료 결제 관련 안내**
> 본 과제에서 Zapier의 Filter by Zapier 및 다중 Action(Gmail + Slack) 기능은 순수 무료 플랜(Free)으로는 사용이 불가하여, **14일 무료 체험판(Trial)**을 통해 구현하였다. 무료 환경만으로 동일한 기능을 구현하려면 Make를 사용하거나, Zapier에서는 단일 Action(Trigger + Action 1개)으로 워크플로우를 단순화하는 방식으로 대체해야 한다.

---

전체 구성을 정리하면 다음과 같습니다.

| 섹션 | 내용 | 상태 |
|------|------|------|
| 1. 개요 | 보고서 목적, 과제 목표, 기능 요구사항 충족 여부 | ✅ |
| 2. 자동화 주제 및 워크플로우 구조 | 공통 구조, 도구별 구현 구조 비교 | ✅ |
| 3. 도구별 구현 과정 요약 | Make / Zapier 각 구현 단계 및 트러블슈팅 | ✅ |
| 4. 비교 분석 | UI/UX, 설정 난이도, 연동 범위, 무료 플랜, 로그 확인, 조건 분기 | ✅ |
| 5. 장단점 정리 | Make / Zapier 각 장단점 | ✅ |
| 6. 적합한 사용 상황 | Make / Zapier 각 추천 상황 | ✅ |
| 7. 결론 | 종합 비교 및 도구 선택 기준 제시 | ✅ |

