# QueryPie PoC

## Kurly Pay 
2022년 8월 10일

### 1. VPN Connect

참조: openvpn 접속 방법.pdf

1. OpenVPN Client Download
https://openvpn.net/client-connect-vpn-for-mac-os/

2. Login
- Username: elon@chequer.io 
- Password: querypie

### 2. PoC Instance(kps-com-ec2-querypie) Connect

```
$ chmod 400 *.pem
$ ssh -i kps-com-querypie-key.pem ec2-user@172.31.8.19 -p 20022
Last login: Wed Aug 10 18:13:16 2022 from ip-172-31-9-15.ap-northeast-2.compute.internal

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|
```

### 3. IAM Assume Role Configuration

```
# IAM 권한 획득을 위해 assume 활용
—
#!/bin/bash
temp_role=$(aws sts assume-role --role-arn "arn:aws:iam::994892398583:role/kps-dev-role-rds" --role-session-name AWSCLI-Session)

export AWS_ACCESS_KEY_ID=$(echo $temp_role | jq -r .Credentials.AccessKeyId)
export AWS_SECRET_ACCESS_KEY=$(echo $temp_role | jq -r .Credentials.SecretAccessKey)
export AWS_SESSION_TOKEN=$(echo $temp_role | jq -r .Credentials.SessionToken)

RDSHOST="kps-dev-rds-cluster.cluster-cdx0dyfemign.ap-northeast-2.rds.amazonaws.com"
TOKEN="$(aws rds generate-db-auth-token --hostname kps-dev-rds-cluster.cluster-cdx0dyfemign.ap-northeast-2.rds.amazonaws.com --port 3306 --username iam_user --region ap-northeast-2)"

mysql --host=$RDSHOST --port=3306 --ssl-ca=/ca/rds-ca-2019-root.pem --user=iam_user --password=$TOKEN —
```

### 4. Aurora RDS 

- Endpoint:
  - Writer: kps-dev-rds-cluster.cluster-cdx0dyfemign.ap-northeast-2.rds.amazonaws.com
  - Reader: kps-dev-rds-cluster.cluster-ro-cdx0dyfemign.ap-northeast-2.rds.amazonaws.com Port: 3306

- Masterusername: admin
- Msterpassword: aprkwhsAprkwhs1


### 5. Security Group

```
aws ec2 describe-addresses --query Addresses[*].PublicIp --region ap-northeast-2

aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].[PublicIpAddress]' --output text
```


- Visual Code
- tmux

- VPN 꺼야 함 
- PEM 400
- querypie 폴더 생성 
- github operation - [9.8.x](https://github.com/chequer-io/operation/tree/main/docker-compose/querypie/9.8.x/type-poc) 
- ping 8.8.8.8

- vi docker-compose # yaml 확장자를 붙이지 않아야 붙여 넣기가 제대로 됨
- querypie db(mysql)를 컨테이너로 실행
- Aurora RDS Write Endpoint만 연결
- nc 설치
```
sudo su - 
yum install -y nc
nc -vz $RDS_HOST 3306
```
- RDS 접속 & 계정 권한 부여
```
mysql -h $RDS_HOST -u admin -p
show databases;
use querypie;
select @@charater_set_database, @@colation_database;

create user querypie identified by 'querypie';
grant all privileges on querypie.* to querypie'%'; 
flush privileges;
```

- run-tool.sh
```
chmod +x run-tool.sh
```
> `run-tool.sh` 실행하면 계속 대기 상태에 있음 

에러 
- Moris Hong에게 문의
- QA 검증한 것 


