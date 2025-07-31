보통 재해라는건 가용영역에서 발생하므로 리전을 옮기거나 이중화로 구현

## DR

<img width="1442" height="564" alt="image" src="https://github.com/user-attachments/assets/5057078a-3af5-4fd3-9e38-91642de1e0ea" />

- **RPO**(Recovery Point Objetive) : 복구 시점 목표
    - 얼마나 자주 백업을 실행할지, 시간 상 어느 정도 과거로 되돌릴 수 있는지
    - 데이터 손실은 얼마나 감수할 것인지 설정
- **RTO**(Revoery Time Objective) : 복구 시간 목표
    - 재해 발생 후 복구할 때 사용

## DR 전략

<img width="1314" height="662" alt="image" src="https://github.com/user-attachments/assets/9316f994-92bf-4354-ad70-0413d26bb321" />

### **1. 백업 및 복구**

- S3에 정기 백업, 장애 시 수동 복원, 저렴하지만 느린 복구
- S3 Lifecycle + Glacier로 구현

<img width="842" height="404" alt="image" src="https://github.com/user-attachments/assets/f356ca6b-3c4f-4e39-8553-c5943166d966" />

### **2. 파일럿 라이트**

- 핵심 컴포넌트만 최소 사양으로 구동
- 백업과 비슷하지만 핵심 컴포넌트가 이미 가동 중이기 때문에 더 빠름
- 단, RDS의 복원만 빠를 뿐, EC2는 AMI로 재생산해야 함
- 리전 단위 트래픽 전환은 Route 53가 담당한다.

<img width="990" height="488" alt="image" src="https://github.com/user-attachments/assets/eb830409-6e06-4938-8800-e03c7fe60000" />

### **3. 웜 대기**

- 최소한의 인프라와 DB가 항시 대기 중, (EC2는 소형, DB는 복제본으로), 실시간 완벽 복구는 X

<img width="976" height="490" alt="image" src="https://github.com/user-attachments/assets/4ef14da4-32ab-463c-86d7-c29885e39bd2" />

### **4. 핫 사이트/다중 사이트**

- 두 개 이상의 리전에 동일한 시스템을 대기

<img width="956" height="494" alt="image" src="https://github.com/user-attachments/assets/9b9cb9c7-6de3-40d6-bbf5-38d1779ac54f" />

### 멀티 리전

- 두 개 이상의 리전에 동일한 시스템을 동시 운영

## DR 팁

**백업**

- EBS, S3는 스냅샷 사용
- S3, S3 IA, S3 Glacier로 주기적 백업, 교차 리전 복제
- 온프레미스 → 클라우드 데이터 공유는 Stoarge Gateway나 Snowball

**고가용성**

- 장애 발생시 Route53로 DNS를 다른 리전으로 전환
- Mutil AZ

**복제**

- 리전간 RDS 복제

**자동화**

- CloudWatch 경보가 실패하면 EC2 복구/재시작

## DMS (Database Migration Service)

빠르고 안전하게 DB를 AWS로 마이그레이션 하며 복원성이 좋고 자가 복구 기능을 제공

마이그레이션 중에도 원본 DB 사용 가능

복제 작업을 수행하기 위한 중간 EC2 필요

## AWS Backup

완전 관리형 서비스로 AWS 서비스 간 백업을 자동화 관리

EC2, EBS, S3, DB, EFS, FSx, Storage Gateway 등 지원

## MGN (Application Migration Service)

어플리케이션을 AWS로 간편하게 마이그레이션 하는 리호스팅 솔루션

물리/가상/클라우드 서버를 AWS에서 네이티브로 실행할 수 있도록 변환

Application DIscovery Service는 마이그레이션을 위해 온프레미스 정보 수집 지원
