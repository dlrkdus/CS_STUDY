## 요금제

온디맨드란? → 필요할 때 즉시 생성하고 사용한만큼 요금이 부과되는 요금제. All Upfront 옵션 쓰면 예약 기간 내 비용을 선불로 지불해 좀 더 할인됨

EC2 : 온디맨드 확장 가능 컴퓨팅 서비스

인스턴스 타입

- 범용, 컴퓨팅 최적화, 메모리 최적화, 스토리지 최적화

물리 호스트 : AZ 내부의 한 대의 실제 서버, ap-northeast-2a 같은 거

EC2 Spot Instance : 남는 자원을 저렴하게 파는 요금제, 동일 AZ 내에서 AWS가 자원이 필요하면 언제든 회수 가능 (2분 전 알림을 주므로 graceful shutdown 가능), 비용에 민감하지만 안정성은 중요하지 않은 작업에 유용

EC2 Spot Fleet : Spot Instance들을 자동으로 관리해줌, 지정한 가격대를 초과하면 인스턴스 종료 가능

## Placement Group

배치 그룹 : 단일 AZ 내에서 인스턴스를 물리적으로 어디에 배치할지, 기본 옵션은 분산형

<img width="1657" height="452" alt="image" src="https://github.com/user-attachments/assets/7f6facd6-39c6-4792-a17c-a703d03cb5b9" />

- **클러스터** : 동일 호스트 혹은 같은 물리 호스트 근처에 배치, 인접하게 배치한다는 것은 같은 인프라 리소스를 사용한다는 것이므로 고가용성과는 반대, 고성능 컴퓨팅, 저지연 네트워크용

<img width="1206" height="909" alt="image" src="https://github.com/user-attachments/assets/74325857-4ffa-4630-acdd-9ae4173c8d91" />

- **파티션** : 인스턴스를 파티션 단위로 묶어서 각 물리 호스트에 배치, 파티션으로 장애 도메인 분리, Kafaka 등

<img width="1041" height="886" alt="image" src="https://github.com/user-attachments/assets/a6f50ed2-817a-4a76-b40d-1a6816959c98" />

- **분산형** : 각각의 인스턴스를 서로 다른 물리 호스트에 배치, 고가용성

# 네트워크

## IP

- public ip : 공인 IP, 인터넷 상에서 고유
- private ip : 사설 IP, 네트워크 내에서만 고유, 다른 네트워크끼리는 겹칠 수 있음. NAT를 통해 통신 가능
- NAT : 공인 IP와 사설 IP를 매핑해주는 작업, SNAT는 내부 → 외부 요청 시, DNAT는 외부 → 내부 요청 시 IP 매핑

따라서 NAT Gateway는 퍼블릭 서브넷에 설치해서 프라이빗 서브넷과의 IP 매핑을 담당함

- 탄력적 IP : 인스턴스는 시작마다 퍼블릭 IP가 변경되므로 고정 퍼블릭 IP 제공, 당연하게도 ASG와 결합이 좋지 않음. EC2는 프라이빗 IP만 갖고 로드밸런서가 퍼블릭 IP를 갖는 것이 일반적이다.

## ENI

인스턴스 생성 시 자동으로 부여되는 네트워크 인터페이스(가상 LAN 카드), IP 주소는 ENI에 부여되는 것.

Private/Public IP, MAC 주소, Subnet 정보, 보안 그룹 등이 모두 ENI에 속한다.

## 그 외

보안 그룹 : 가상 방화벽, 타임아웃이 뜬다는 것은 보안 그룹에 막힌 것. connection refused는 보안 그룹은 통과했지만 애플리케이션 문제인 것

User data : 인스턴스 초기 설정용 스크립트

EC2 Hibernate : EC2를 재부팅하는 것은 비싼 일이므로 최대 절전 모드를 활성화하면 인메모리가 유지되며 부팅 속도가 빨라진다. 인메모리 상태는 루트 경로의 EBS 볼륨에 기록되고, 따라서 암호화돼야 한다.
