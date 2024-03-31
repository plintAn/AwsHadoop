On-prem 환경에서 Cloud 환경으로 마이그레이션하는 과정에서 공부를 위해 작성합니다.

# AwsHadoop

### Cloud 환경 구성

| 하둡 주요 프레임워크 | AWS 주요 서비스 | 기타 사용 프로그램 | 주요 사용 언어 |
|----------------------|-----------------|---------------------|----------------|
| - HDFS               | - EC2            | - Oracle            | - Python       |
| - YARN               | - S3             | - PostgreSQL        | - SQL(Ansi)    |
| - Spark              | - Lambda         | - DBeaver           |                |
| - HiveDB             | - Glue Studio    |                     |                |
| - Sqoop              | - EMR Cluster    |                     |                |
|                      | - MWAA(Airflow)  |                     |                |
|                      | - Athena         |                     |                |
|                      | - SageMaker      |                     |                |
|                      | - IAM            |                     |                |

### On-prem 환경 구성

| 하둡 주요 프레임워크 | 주요 서비스 | 주요 사용 언어 |
|----------------------|-------------|----------------|
| - HDFS               | - Oracle    | - Python       |
| - YARN               | - Orange    | - SQL(Ansi)    |
| - Spark              | - MySQL     |                |
| - HiveDB             | - DBeaver   |                |
| - Sqoop              | - PostgreSQL|                |
| - Ambari             |             |                |
| - HBase              |             |                |
| - Oozie              |             |                |
| - HadoopCluster      |             |                |

기본적은 구성은 다음과 같고, 크게 달라진 부분은 Hadoop Cluster => Aws Emr Cluster 변경과
이에 따른 Spark, MapReduce 환경 세팅도 Emr Cluster로 변경되었고 Python 코드도 추가되었습니다.
또한 클러스터 관리 프레임워크는 기존 Ambari, Oozie, Hbase => Mwaa(Airflow)으로 변경되어 보다 나은
UI 제공과 간편화된 시스템을 구축하게 되었다. 

하둡은 분산저장(HDFS), 분산처리(MapReduce의 결합이다
ㄴ분산처리:MapReduce 프레임워크
Map은 데이터 처리, Reduce함수는 원하는결과 추출

### 클라우드 활용 빅데이터 처리 프로세스

* 1.DPCC(원천DB)는 매일 00:00~00:10 복제되어 Bcv(BPBB)라는 원천 복제 DB를 만든다.
* 2.BCV(Oracle DB) 데이터를 Sqoop을 통해 Hadoop 내 HDFS(HiveDB)으로 옮긴다.
  - 파일 데이터는 Block 단위로 쪼개지고
  - Hadoop Cluster 저장된 데이터는 Yarn(리소스 관리)에 의해 각 노드에 분산 돼 저장된다.
* 3.Hadoop Cluster => EMR Cluster Sqoop
  - 사용자(DataAnalysis)들의 요구에 의해 진행됨.
  - EMR Cluser 환경 세팅(HDFS, MapReduce, Spark app, Yarn(리소스관리),HiveDB(SQL)
* 4.EMR Cluster에 저장된 데이터는 MapReduce는 AWS SDK(boto3) python 코드를 통해 마스터 노드에 작업 제출
  - 4-1.Job Submission:마스터 노드에 작업 제출
  - 4-2.Job Splitting:여러 개의 task로 분할
  - 4-3.Map Phase:데이터를 키-값으로 매핑, 중간 결과 생성
  - 4-4.Shuffle and Sort:데이터 재배치 정렬
  - 4-5.Reduce Phase:중간 결과를 통해 최종 결과물 생성
  - 4-6.Job Completion:지정 장소에 저장됨
* 5.EMR Cluster에 저장된 데이터는 AWS CLI나  EMR Console 사용하여 Spark 사용
  - 4-1 Map(데이터 처리), Reduce(원하는결과 추출)
  - 4-2.Spark RDD(분산 저장 데이터)들을 다양한 연산(map, filter, reduce, join)을 활용하여 가공하고
  - 필요한 형식으로 변환하여 결과 생성됨 (Spark는 SQL Where 절을 통해 원하는 데이터 입수 가능).
  - EMR은 스파크 기반의 빅데이터 처리 및 분석에 특화
* 6.Athena 쿼리를 통해 EDA(분석), Glue Studio(카탈로그), SageMaker(ML) 사용

  
















  
