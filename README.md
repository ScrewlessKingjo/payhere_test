# payhere_test

## 요구사항
  - **상점의 30분 전까지의 집계 데이터를 제공해야 한다.**
      - 집계 데이터 종류: 결제 금액 합산, 상품 수
  - **서비스 DB의 부하를 최소화해야 한다.**
  - **AWS 혹은 GCP 클라우드 환경을 고려하여 설계해야 한다.**

이상의 조건에 따라 **GCP 클라우드 환경 기반의 CDC 아키텍처**를 설계하였습니다.

---

## 아키텍처 개요

### **1. Cloud Pub/Sub**
- **역할**: CDC(Change Data Capture) 기반으로 상점 결제 데이터의 변경 사항을 감지하고 메시지 큐로 전송
- **이유**: 서비스 DB의 부하를 최소화하면서 실시간 데이터 처리를 가능하게 함

### **2. Google Dataflow**
- **역할**: Pub/Sub에서 수신한 데이터를 30분 단위로 집계하여 BigQuery에 적재
- **이유**: 실시간 스트리밍 처리를 통해 BigQuery의 연산 부담을 줄이고, 최신 데이터를 빠르게 반영

### **3. Google BigQuery**
- **역할**: Dataflow에서 집계된 데이터를 저장하고 분석
- **이유**: 초대용량 데이터를 빠르게 조회할 수 있는 DW 환경 제공

### **4. Looker**
- **역할**: BigQuery의 데이터를 활용하여 대시보드 시각화
- **이유**: 데이터 소비자가 직관적으로 데이터를 탐색하고 분석할 수 있도록 지원

---

## 기술 선정 이유

1. **실시간성을 확보하기 위해 CDC 아키텍처를 적용**  
   - Batch 방식으로 BigQuery에서 직접 집계를 수행할 경우 서비스 DB 부하 증가 및 실시간성이 저하될 가능성이 있음  
   - 따라서 **Cloud Pub/Sub을 활용한 실시간 CDC 기반 아키텍처**를 채택하여 빠른 데이터 반영을 보장

2. **BigQuery의 연산 부담을 최소화하기 위해 Dataflow 활용**  
   - BigQuery에서 직접 집계를 수행하면 비용이 증가할 가능성이 있음  
   - Dataflow를 활용해 **미리 집계된 데이터를 저장**하여 BigQuery의 조회 비용을 절감

3. **비즈니스 데이터 시각화를 위해 Looker 도입**  
   - 데이터 소비자가 원하는 지표를 쉽게 분석할 수 있도록 Looker를 연계하여 대시보드 제공
