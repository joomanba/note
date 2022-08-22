## Computing
### EC2

## Database

### 메모리 최적화된 DB 인스턴스 클래스
- 참조: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.DBInstanceClass.html
- db.r3 : 메모리 최적화를 제공하는 인스턴스 클래스
- db.r4 : 추가적인 향상된 네트워킹 성능 제공 

### Aura Serverless
#### ACU(Aurora Capacity Unit)

- 처리(CPU) 용량과 메모리(RAM) 용량의 조합
- 프로비저닝된 워크로드에 T3 또는 T4g와 같은 소규모 DB인스턴스 클래스에 비해 너무 높은 메모리 요구사항이 있는 경우 R5 또는 R6g DB 인스턴스에 필적하는 메모리를 제공하는 최소 ACU 설정을 선택 (성능 개선 도우미 - ACU 2개)
- 1 ~ 256 ACU
- Small  : T3 ~ T4 
- Medium : R5 ~ R6 
  - 통상적으로 r5 xlarge (4vCPU) (최소 16기가 메모리가 있어야 데이터 올리고 내리고가 원활)
  - 크리티컬 서비스다 싶으면 r6 8xlarge (32vCPU)로 시작 
- Large  : 성능 개선도우미 보고 자원 쓰는거 보고 결정
