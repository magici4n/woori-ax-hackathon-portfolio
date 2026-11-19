# 최예성 | 백엔드 개발 포트폴리오

## 절세 WON해? · 대화로 매도 조건을 정하는 해외주식 절세 시뮬레이터

**우리WON 청년 IT 아카데미 AX 해커톤 · 4인 팀 · 2박 3일 · 로컬 프로토타입**

해외주식 투자자가 “테슬라는 팔지 않을게요”라고 말하면, 해당 종목을 매도에서 제외하는 조건을 제안합니다. 사용자는 조건을 확정한 뒤 매도 조합별 예상 세금과 남는 자산을 비교할 수 있습니다.

### 맡은 역할

팀에서 개발을 주로 맡았습니다. 자산을 먼저 확인하고, 챗봇에서 매도 조건을 정한 뒤, 시나리오를 비교하는 흐름을 제안했습니다. 팀원들은 기획, 디자인·퍼블리싱과 산출물 관리, 발표와 법적 검토를 담당했습니다.

### “테슬라 매도 제외만 풀어줘”를 처리하는 과정

챗봇을 사용하다가, 이미 제외한 종목을 다시 매도하려면 직접 설정 화면으로 이동하라는 안내를 발견했습니다. 저는 대화 안에서 그 종목의 제외를 해제할 수 있도록 변경을 요청했습니다.

수정 전에는 모델이 해제를 제안해도 서버가 기존 조건과 충돌한다고 판단해 거부했습니다. 수정 후에는 모델이 바꿀 종목과 항목을 제안하면 서버가 기존 조건에 해당 변경만 반영합니다. 사용자의 뜻이 모호하면 확인 질문을 하고 적용을 보류합니다. [수정 커밋](https://github.com/magici4n/woori-ax-hackathon/commit/00630023c7e595be3365d3776566d4f5e7da632f)

| 검증한 상황 | 기대 동작과 확인 결과 |
| --- | --- |
| 테슬라와 나이키가 모두 매도 제외인 상태에서 테슬라 제외만 해제 | 테슬라는 매도 가능해지고, 테슬라의 수량·비율 한도와 나이키 제외 조건은 유지됐습니다. [조건 변경 테스트](https://github.com/magici4n/woori-ax-hackathon/blob/main/backend/src/test/java/com/woori/tax/AssistantServiceTest.java#L70-L83) |
| 테슬라 제외 조건이 유효한데 모델이 테슬라 1주 매도를 제안 | Java가 사용자 제한 위반으로 제안을 거부했습니다. 같은 매도안도 제외 조건을 풀면 계산할 수 있습니다. [시나리오 검증 테스트](https://github.com/magici4n/woori-ax-hackathon/blob/main/backend/src/test/java/com/woori/tax/PortfolioSimulationServiceTest.java#L68-L79) |

### 백엔드에서 제안을 계산 결과로 바꾸는 방법

React 화면에서 정한 조건과 최근 대화, 보유 종목, 기사·공시 근거를 Spring Boot 서버가 OpenAI Responses API에 전달합니다. 모델이 제안한 매도 수량은 서버에서 보유량·매도 한도와 대조합니다. 검증을 통과한 수량으로 Java가 손익·수수료·예상 세금을 계산합니다. 제안을 검증할 수 없으면 사용자는 기본 계산안을 선택할 수 있습니다. [제안 검증과 계산 코드](https://github.com/magici4n/woori-ax-hackathon/blob/main/backend/src/main/java/com/woori/tax/PortfolioSimulationService.java#L62-L81)

### 확인한 범위와 다음 과제

위 두 사례는 해커톤 당시 자동 테스트 실행 기록에서 통과를 확인했습니다. 별도로, 제가 요청한 시나리오 생성 API 실호출 한 건에서도 두 매도안 모두 테슬라 제외 조건을 지켰습니다. 시연에는 가상 계좌와 JSON 데이터를 사용했고, 배포·실사용자 검증·성능 측정은 진행하지 않았습니다.

단일 요청을 올바르게 처리해도 여러 번 바꾼 조건이 마지막 계산까지 유지되는지는 별도의 검증이 필요합니다. 다음 과제는 한 대화에서 제외·해제·한도 변경을 차례로 요청하고, 최종 매도안을 마지막으로 확정한 조건과 대조하는 것입니다.

[팀 프로젝트 저장소](https://github.com/magici4n/woori-ax-hackathon) · [로컬 실행 방법](https://github.com/magici4n/woori-ax-hackathon/blob/main/README.md) · [시연 순서](https://github.com/magici4n/woori-ax-hackathon/blob/main/docs/DEMO.md)
