# Cloud 환경에서의 Hadoop 마이그레이션

## 환경 구성

### AWS 서비스 활용

| 하둡 주요 프레임워크 | AWS 주요 서비스   | 기타 사용 프로그램 | 주요 사용 언어 |
|----------------------|-------------------|---------------------|----------------|
| - HDFS               | - EC2              | - Oracle            | - Python       |
| - YARN               | - S3               | - PostgreSQL        | - SQL(Ansi)    |
| - Spark              | - Glue Studio      | - DBeaver           |                |
| - HiveDB             | - RDS(Mysql)       | - Mysql             |                |
| - Sqoop              | - EMR Cluster      |                     |                |
|                      | - MWAA(Airflow)    |                     |                |
|                      | - Athena           |                     |                |


### 변경 사항

- Hadoop Cluster => AWS EMR Cluster로 변경
ㄴSpark 및 MapReduce 환경도 EMR Cluster로 이관
- Oozie => MWAA(Air Flow for aws)
- Hive Query => Athena Query
- Oracle DB => S3

### 클라우드 활용 빅데이터 처리 프로세스

1. **데이터 복제 및 이동**
   - DPCC(원천DB)는 매일 특정 시간에 복제되어 Bcv(BPBB)라는 원천 복제 DB를 생성한다.
   - BCV(Oracle DB) 데이터를 Sqoop을 통해 HDFS(HiveDB)로 이동한다.
     - 파일 데이터는 Block 단위로 분할되며, Hadoop Cluster 저장된 데이터는 Yarn에 의해 분산 저장된다.

2. **환경 설정 및 데이터 처리**
   - EMR Cluster에서 사용자(DataAnalysis)들의 요구에 따라 환경을 설정한다 (HDFS, MapReduce, Spark app, Yarn, HiveDB).
   - AWS SDK(boto3) Python 코드를 사용하여 EMR Cluster에 저장된 데이터에 대한 작업을 제출한다.
   
   **부가 설명:** EMR은 확장 가능하고 관리가 용이한 서비스로, 대규모 데이터 처리를 위한 클러스터를 신속하게 설정할 수 있다. 이는 비즈니스 요구사항에 따라 적응 가능한 유연한 환경을 제공한다.

3. **작업 수행**
   - Job Submission: 작업을 마스터 노드에 제출한다.
   - Job Splitting: 여러 개의 task로 분할한다.
   - Map Phase: 데이터를 키-값으로 매핑하여 중간 결과를 생성한다.
   - Shuffle and Sort: 데이터를 재배치하고 정렬한다.
   - Reduce Phase: 중간 결과를 통해 최종 결과물을 생성한다.
   - Job Completion: 결과물이 지정된 장소에 저장된다.

4. **Spark 활용**
   - 저장된 데이터에 대한 Spark 작업을 AWS CLI나 EMR Console을 통해 수행한다.
   - Spark RDD를 활용하여 데이터를 가공하고 원하는 형식으로 변환하여 결과를 생성한다.
   - Spark는 SQL Where 절을 사용하여 데이터 필터링이 가능하며, EMR은 스파크 기반의 빅데이터 처리 및 분석에 특화되어 있다.

5. **기타 서비스 활용**
   - Athena를 통한 EDA(분석), Glue Studio를 활용한 데이터 카탈로그, SageMaker를 활용한 머신러닝 작업을 한다.
