## MySQL 설치

```
# root 권한 변경
sudo -i

# Selinux Disable
setenforce 0

# MySQL 설치

pushd /tmp
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.20-1.el7.x86_64.rpm-bundle.tar
tar xvf mysql-8.0.20-1.el7.x86_64.rpm-bundle.tar
yum -y install mysql-community-*.rpm
rm -rf *.rpm
rm -rf rpm-bundle.tar
systemctl enable mysqld
systemctl start mysqld

# 임시 패스워드 확인
cat /var/log/mysqld.log | grep "temporary password"  

2022-06-03T05:17:21.200459Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: Eqg0hpGTlg%g

mysql -uroot -p

# 지금부터는 MySQL Shell 내부에서 실행
mysql>

alter user 'root'@'localhost' identified by 'Querypie1!';

create database `querypie` character set 'utf8mb4' collate 'utf8mb4_general_ci'; 
create user 'querypie' identified by 'Querypie1!';

grant all privileges on querypie.* to querypie@'%';

exit;
```

## Docker 및 Docker Compose 설치

```
# Docker 설치

amazon-linux-extras install -y docker

# 도커 유저 설정
usermod -aG docker ec2-user

# 도커 데몬 서비스 설정
systemctl enable --now docker 
systemctl start docker

# 도커 프로세스 확인
ps -ef | grep docker

root      3841     1  0 05:22 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --default-ulimit nofile=32768:65536
root      3980  3170  0 05:22 pts/0    00:00:00 grep --color=auto docker

# Docker Compose 설치
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
sudo chmod +x /usr/bin/docker-compose

# Docker Compose 설치 확인
docker-compose -version
```

## Docker Compose 실행을 위한 설정파일 다운로드 및 Docker Registry 로그인
- 지금부터는 ec2-user 계정에서 실행해야함. root로 되어 있다면 exit으로 ec2-user로 복귀

```
# 홈 디렉토리 생성 및 Docker Compose 설정 파일 다운로드

mkdir -p ~/querypie

cd querypie
curl https://dl.querypie.com/docker-compose -o docker-compose.yml
curl https://dl.querypie.com/nginx -o nginx.conf
sudo curl https://dl.querypie.com/logrotate -o /etc/logrotate.d/querypie

-- 그라파나는 추후 설정
#curl https://dl.querypie.com/grafana.ini
#sudo curl https://dl.querypie.com/prometheus.yml
#

# Docker Repository 로그인
# 계정정보는 brant@chequer.io로 문의

docker login dockerpie.querypie.com
# Username: 입력
# Password: 입력

docker login시 아래와 같은 메시지가 뜨면, SSH를 다시 접속하면 된다.

Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/auth": dial unix /var/run/docker.sock: connect: permission denied
```

## QueryPie 설치에 필요한 환경변수 설정
```
vi $HOME/.querypie.rc

export DB_HOST="localhost"
export DB_PORT="3306"
export DB_CATALOG="querypie"
export REDIS_HOST="127.0.0.1"
export REDIS_PASSWORD="*]BD3r4HP{_9e<x_"
export DB_USERNAME="querypie"
export DB_PASSWORD="Querypie1!"
export API_HOST="127.0.0.1"
export VERSION="{VERSION}"
export ARISA_HOST="127.0.0.1"
export ARISA_ADVERTISE_HOST="{EC2-PUBLIC-IP}"
export TOOLS_HOST="{EC2-PUBLIC-IP}"
export TOOLS_PORT="3000"
export QUERYPIE_WEB_URL="http://{EC2-PUBLIC-IP}"

<ESC> :wq 

echo "source $HOME/.querypierc" >> $HOME/.bashrc
```

- VERSION은 PoC 가능한 버전을 입력 (9.8.5 버전까지만 배포가능!)

## Docker-Compose 를 통해 QueryPie Tools 실행하기
```
. ~/.bashrc 
cd $HOME/querypie 

docker-compose pull
docker-compose --profile tools up
```

## QueryPie DB 초기화
브라우저에서 `http://{EC2-PUBLIC-IP}:3000`로 접속하여 `MIGRATE` 버튼을 눌러서 초기 데이터베이스를 셋팅

## QueryPie Docker-Compose 실행
```
sudo mkdir -p /var/log/querypie
sudo chown ec2-user:ec2-user /var/log/querypie
docker-compose --profile production -d up
```

## QueryPie Proxy 설정
```
mysql -uquerypie -p

MySQL>

use querypie;
update proxies set company_uuid=(select company_uuid from companies where id='1') where id='1';
update company_settings set agent_enabled=1 where id=1; 
update proxies set host='[ec2-ip]', agent_port=9000, port_from=40000, port_to=41000 where id=1;
```

- 브라우저에서 http://{EC2-Public-IP}로 접속, admin / querypie로 로그인


## 프로세스 및 로그 확인
- Docker 프로세스 확인: `docker ps -a`
- Docker-Compose 프로세스 확인: `docker-compose ps`
- 특정 컨테이너 프로세스 로그 확인
  - `docker log -f {CONTAINER_ID}`
- 특정 Docker 컨테이너 내부로 접속하기 : `docker exec -it {COINTAINER_ID} bash`



