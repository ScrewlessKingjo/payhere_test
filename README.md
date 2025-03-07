# payhere_test

## 요구사항
  - **상점의 30분전까지의 집계 데이터를 보여줘야 한다.**
      - 집계 데이터 종류 : 결제 금액 합산, 상품 수
  - **단, 실 서비스에 영향이 없어야 한다.**
  - AWS 혹은 GCP 클라우드 환경을 고려할 것

이상의 조건에 따라 GCP 클라우드 환경 기반으로 설계하였습니다.

## 아키텍처 개요

### 1. Cloud Pub/sub
CDC 기반으로 데이터 변경 사항을 감지하여 메시지 큐로 전송
### 2. Google Dataflow
Pub/Sub에서 받은 데이터를 변환 및 집계 후 BigQuery에 적재
### 3. Google Bigquery
집계된 데이터를 저장 및 분석
### 4. Looker 
BigQuery 데이터를 기반으로 대시보드 시각화

## 기술 선정 이유

1. Bigquery 기반의 Batch 작업 위주로 설계할 경우, 구조적으로 단순해지기 때문에 유지보수의 용이함은 증가하지만, 실시간성이 줄어들며 서비스 DB에 부하를 가중시킬 수 있기 때문에 Pub/Sub 기반의 CDC 아키텍처로 설계하였습니다.
2. Bigquery에서의 조회 및 연산 작업을 최소화하여 비용 이슈를 최소화하기 위해 Dataflow를 추가하였습니다.
3. 데이터 수요자가 해당 데이터를 시각적으로 확인할 수 있도록 Looker를 추가하였습니다.
