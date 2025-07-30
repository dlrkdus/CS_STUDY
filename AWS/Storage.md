객체와 파일의 차이 

객체 : 파일 + 메타데이터 + 고유 ID, 평면적 (버킷에 객체 저장, backup_2024/image_01)

파일 : 디렉토리 + 파일, 계층적(폴더에 저장, /home/data/a.txt)

객체는 메타 데이터, 버전 관리가 S3의 주요 차이점, Http로 접근한다.

S3에 접근하기 위해선 IAM 정책이 있어야하고, 버킷 정책과 ACL로 접근 권한 설정이 가능하다.

## 객체 스토리지

- S3 : 정적 파일, 이미지 저장 등에 사용, 비용 효율적, 확장성
- S3 **Glacier** : 거의 사용하지 않지만 삭제하면 안되는 데이터 장기 보관용
    - GDA : 장기 보관, 접근에 최대 72시간 소요
    - IR : 즉시 접근 가능, 가격이 더 비싸다
- S3 **TA** : 인터넷 구간은 짧게, AWS 네트워크 기간은 길게 해 전 세계 사용자에게 빠른 속도 지원

### 객체 스토리지 옵션

- **Legal Hold** : 객체 무기한 보존
- **Storage Lens** : S3 스토리지 사용량 및 최적화 리포트
- **멀티 파트 업로드** : 대용량 객체를 나눠서 병렬 업로드
- **배치 작업** : 대규모 객체 작업의 공통 작업 (암호화 등)을 일괄 자동화하는 기능
- **Storage Gateway** : 온프레미스와 S3 간 연동 게이트웨이
- **Intelligent Tiering** : 불규칙한 객체 접근 패턴에 따라 자동으로 요금 최적화

### 객체 버전 관리

버전 관리 옵션을 활성화해서 사용, 기존 파일을 덮어쓰기 해도 예전 버전이 남음

객체마다 고유한 버전 ID가 부여된다. 실수로 덮어쓰거나 삭제한 파일 복구 가능, 스토리지 비용 증가

### S3 보안

SSE : 서버측 암호화, CSE : 클라이언트측 암호화

- SSE-S3 : AWS가 관리하는 키를 사용해 암호화, 기본 옵션
- SSE-KMS : AWS KMS로 관리되는 키를 사용해 암호화
- SSE-C : AWS 외부에서 관리되는 키를 AWS로 보내 암호화, HTTPS
- CSE : 클라이언트 라이브러리 사용

**CORS** : Cross-Origin Resouce Sharing, Origin이란 https://www.example.com과 같이 도메인의 출처

<img width="2048" height="852" alt="image" src="https://github.com/user-attachments/assets/66c008c3-cd8b-4173-ba6f-6199c56603c3" />

웹 브라우저에서 메인 오리진을 방문하면서 다른 오리진으로의 요청을 허용하기 위한 기능

<img width="2048" height="547" alt="image" src="https://github.com/user-attachments/assets/7b2acf52-047a-4fdb-ae72-9f6b22299447" />

클라이언트가 Preflight 요청을 보내면 서버 측이 **Access-Control-Allow-Origin** 같은 요청 허용 헤더로 응답해야 함, S3도 마찬가지로 CORS 헤더를 설정해야 교차 출처 요청이 허용됨

**MFA**

중요한 S3를 삭제하기 전에 이중 보안, 버전 관리를 활성화해야 사용할 수 있음

**Pre-Signed URL**

로그인한 사용자에게 만료 기간동안 GET/PUT을 허용하는 URL

### S3 복제

리전 장애 시 DR 수단

소스 버킷과 복제 대상 버킷 모두 버전 관리 기능이 활성화돼있어야 함

- **Cross-Region Replication (CRR)**: 교차 리전 복제
- **Same-Region Replication (SRR)**: 같은 리전으로 복제

## 파일 스토리지

- EFS : 다중 AZ에서도 여러 EC2가 네트워크로 접근하는 NFS 시스템. 리눅스 전용, 서버 간 실시간 파일 공유에 사용, 비용 비쌈
- FSx : 고성능 파일 시스템 → EFS는 리눅스 기반 NFS지만 FSx는 다양한 OS 타입 제공
    - Windows
    - Lustre : HPC/병렬 처리 최적화
    - NetApp ONTAP : NAS 기반
    - OpenZFS : ZFS 기반

## 블록 스토리지

- EBS : EC2 인스턴스에 붙은 블록 스토리지
    - CCP 레벨 : EC2와 1:1 마운트
    - Associate 레벨 : EC2와 1:N 마운트
    - EBS Multi Attach : 동일 AZ 내의 EC2 인스턴스들이 하나의 고성능 볼륨을 공유하는 것, 클러스터링 환경에서 사용
- EBS 볼륨 : 인스턴스 디스크, 종료 후에도 데이터 유지, 루트 볼륨에는 OS 정보가, 추가 볼륨에는 DB와 로그 저장, 특정 AZ에 바인딩
    - gp(general purpose), io는 SSD고 st, sc는 HDD다
- EBS Snapshot : EBS 볼륨 백업을 S3에 저장, 빠른 복구 가능

고성능 하드 디스크가 피료한 경우 EC2 인스턴스 스토어 사용

EBS를 옮기려면 스냅샷을 만들어 다른 AZ에서 스냅샷을 복원해 볼륨을 생성해야 함
