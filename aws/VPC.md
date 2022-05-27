# VPC

- AWS에서 제공하는 가상 네트워크.

## VPC의 특징

- 계정 생성 시 default로 VPC를 만들어줌
- EC2, RDS, S3등 서비스 활용 가능
- 서브넷 구성
- 보안 설정(IP block, inbound, outbound 설정)
- VPC Peering(VPC 간의 연결)
- IP 대역 지정 가능
- VPC는 하나의 Region에만 속할 수 있음(다른 Region으로 확장 불가능)

## VPC의 구성요소

- Availability Zone
- Subnet(CIDR)
- Internet Gateway
- Network Access Control List/security group
- Route Table
- NAT(Network Address Translation) instance/NAT gateway
- VPC endpoint

### Availability Zone

- 물리적으로 분리되어 있는 인프라가 모여 있는 데이터 센터
- 각 AZ는 일정 거리 이상 떨어져 있음
- 하나의 리전은 2개 이상의 AZ로 구성됨
- 각 계정의 AZ는 다른 계정의 AZ와 다른 아이디를 부여받음

### subnet

- VPC의 하위 단위 (sub+network)
- 하나의 AZ에서만 생성 가능
- 하나의 AZ에는 여러 개의 subnet 생성 가능
- private subnet: 인터넷에서 접근 불가능한 subnet
- public subnet: 인테넷에 접근 가능한 subnet
- CIDR 블록을 통해 Subnet을 구분
- CIDR: 하나의 VPC 내에 있는 여러 IP 주소를 각각의 Subnet으로 분리/분배하는 방법

### Internet gateway(IGW)

- 인터넷으로 나가는 통로
- Private subnet은 IGW로 연결되어있지 않다.

### Route table

- 트래픽이 어디로 가야할지 알려주는 테이블
- VPC 생성 시 자동으로 만들어줌.

  - 10.0.0.0/16(10.0.0.0 ~ 10.0.255.255까지) -> Local
  - 나머지는 IGW(인터넷)

  | Destination | Target |
  | ----------- | ------ |
  | 10.0.0.0/16 | Local  |
  | 0.0.0.0/0   | igw-id |
  | ::/0        | igw-id |

### NACL(Network Access Control List)/Security Group

- 보안 검문소
- NACL -> Stateless, SG -> Stateful
- Access Block은 NACL에서만 가능

### NAT(Network Address Translation) instance/NAT gateway

- Private subnet 안에 있는 private instance가 외부의 인터넷과 통신하기 위한 방법
  - NAT Instance는 단일 Instance(EC2)
  - NAT Gateway는 aws에서 제공하는 서비스(서비스)
- NAT Instance는 Public Subnet에 있어야 함

### Bastion host

- Private Instance에 접근하기 위한 수단
- Public subnet 내에 위치하는 EC2

### VPC endpoint

- Aws의 여러 서비스들과 VPC를 연결해주는 중간 매개체
  - Aws에서 VPC 바깥으로 트래픽이 나가지 않고 aws의 여러 서비스를 사용하게끔 만들어주는 서비스
  - Private subnet 같은 경우는 격리된 공간인데, 그 상황에서도 aws의 다양한 서비스들(S3, dynamodb, athena)연결할 수 있도록 지원하는 서비스
- Interface Endpoint: private ip를 만들어 서비스로 연결해줌(SQS, SNS, Kinesis, Sagamaker 등 지원)
- Gateway Endpoint: 라우팅 테이블에서 경로의 대상으로 지정하여 사용(S3, Dynamodb 지원)
