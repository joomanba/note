```
use querypie;
update proxies set company_uuid=(select company_uuid from companies where id='1') where id='1';
update company_settings set agent_enabled=1 where id=1; 
update proxies set host='34.227.78.215', agent_port=9000, port_from=40000, port_to=41000 where id=1;
```



```

안녕하세요.
체커 장의진입니다.
답변이 늦어진 점 양해 부탁드립니다.

쿼리파이 설치를 위한 인프라스펙 및 관련 정보 말씀드립니다.

1. 작업순서
말씀 인프라 스펙에 따라 인프라가 사전에 구성되었을 경우, 아래와 같은 순서로 작업이 진행됩니다.
DB에 저장된 데이터의 크기에 따라 마이그레이션 작업이 1시간 이상 소요될 수도 있습니다.
특별한 이슈가 없으면 c번 쿼리파이 설치는 1시간 이내에 완료 가능합니다.

a. 쿼리파이 중단
b. 쿼리파이 데이터베이스 마이그레이션 
c. 쿼리파이 설치 (Middleware, API, Proxy)
d. 쿼리파이 실행 및 테스트


2. 사전체크리스트

a. EC2, MySQL, ElasticCache 정확한 사양으로 설치되어 있는가?
b. Security Group이 정확하게 설정되어 있는가?
c. Target Group을 포함해서 NLB가 정상적으로 설정되어 있는가?
d. ACM, Domain Name이 정확하게 설정되어 있는가?

```