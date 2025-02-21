# 🏠네덜란드 임대 주택 기관의 집 수리 요청 처리 프로세스 데이터

## 데이터 설명
네덜란드 임대 주택 기관에서 집 수리 요청을 처리하는 프로세스 데이터입니다.  
각 **caseID**는 하나의 수리 요청을 나타내며, 해당 요청에 대해 여러 **taskID**가 수행됩니다.  
각 업무는 특정 **resource(작업자)**가 담당하며, **eventtype**을 통해 업무의 시작(start)과 완료(complete)을 구분합니다.

## 데이터 필드 설명

| 컬럼명 | 설명 |
|--------|------|
| **caseID** | 집 수리 요청 접수 번호 |
| **taskID** | 업무 |
| **originator** | 업무 담당자 |
| **eventtype** | 업무의 시작(start)과 끝(complete)을 표시 |
| **contact** | 집 수리 요청의 요청 채널 |
| **RepairType** | 수리 방식 |
| **objectKey** | 수리 대상 집 key |
| **RepairInternally** | 내부 수리 여부 |
| **EstimatedRepairTime** | 예상 수리 시간 |
| **RepairCode** | 수리 종류 |
| **RepairOK** | 수리 정상 종료 여부 |
| **Date** | 업무 수행 일자 |
| **Time** | 업무 수행 시각 |

이 데이터는 각 수리 요청이 어떤 방식으로 처리되었는지, 예상 수리 시간과 실제 완료 여부 등을 분석하는 데 활용될 수 있습니다.

## 2. 데이터 탐색 (EDA)
### 🛠 Case당 Task 개수 분석
- 대부분의 **caseID**는 **10개의 task**를 거치며 처리됨.
- 일부 case는 9개 또는 11개의 task가 존재.

### 🛠 결측치 분석
- **RepairType, objectKey, RepairInternally** 컬럼에서 **90% 이상의 결측치** 존재.
- **taskID와 originator** 일부 결측 → 삭제 필요.

### 🛠 연도별 수리 요청 추이 (Complete 기준)
- **1905년과 1970년에 비정상적인 데이터(이상치) 존재** → 변환 오류 가능성.
- 2020년 이후 데이터를 분석 대상으로 설정.

---

## 3. 데이터 전처리
### 🛠 이상치 처리
- 동일한 **caseID-taskID가 여러 번 complete된 경우**가 존재 → 중복 제거.
- **System이 담당한 경우 start가 없는 문제** → System 데이터 제거.

### 🛠 내부 수리(InternRepair) 처리 시간 분석
- 내부 수리 담당자는 **John, Cindy**가 많음.
- 내부 수리 소요 시간이 **최대 871분** → **수리 절차 개선 필요**.

---

## 4. 병목현상 분석
### 🛠 프로세스 유형별 정리
| **Process Type** | **Count** | **Flow** | **설명** |
|-----------------|----------|---------|---------|
| Type 1 | 77 | FirstContact → MakeTicket → ArrangeSurvey → Survey → InternRepair → RepairReady → SendTicketToFinAdmin | 내부 수리 프로세스 진행 |
| Type 2 | 69 | FirstContact → MakeTicket → ArrangeSurvey → Survey → ImmediateRepair → RepairReady → SendTicketToFinAdmin | 즉시 수리 프로세스 |
| Type 3 | 63 | FirstContact | 최초 접수 후 중단된 사례 |

### 🛠 병목 구간 분석 (Task 전환 시간 계산)
| **TaskID → Next TaskID** | **평균 대기 시간** |
|------------------------|----------------|
| **MakeTicket → Survey** | **7.9일** |
| **ArrangeSurvey → InternRepair** | **6.8일** |
| **InformClientSurvey → Survey** | **3.5일** |
| **Survey → InternRepair** | **1.3일** |

---

## 5. 결론 및 개선 방안 🚀
### 🔍 주요 병목현상 및 해결 방안
| **문제점** | **원인** | **개선 방안** |
|------------|----------|--------------|
| **MakeTicket → Survey 지연 (7.9일)** | 조사 일정 배정 지연 | 조사 일정 자동화 및 조정 프로세스 개선 |
| **InternRepair 시작 지연 (6.8일)** | 기술자 배정 및 부품 준비 문제 | 기술자 배정 최적화, 부품 사전 준비 |
| **InformClientSurvey 이후 작업 지연 (3일 이상)** | 고객 응답 대기, 일정 조율 문제 | 고객 응답 자동화 및 사전 일정 조율 |
| **즉시 수리(ImmediateRepair) 담당자별 처리 시간 차이** | 특정 담당자의 속도 차이 | 교육 및 업무 표준화 |

### 🏆 기대 효과
✅ 수리 프로세스의 병목 제거  
✅ 내부 수리 및 즉시 수리 속도 개선  
✅ 담당자별 업무 속도 표준화  
✅ 조사 일정 배정 자동화로 처리 시간 단축  
✅ FirstContact 이후 첫 작업까지의 대기 시간 감소  
