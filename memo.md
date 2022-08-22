
## stern
https://www.howtogeek.com/devops/how-to-monitor-kubernetes-pod-logs-in-real-time-with-stern/
stern . -n broadpeak --container 'composite.*'

## kubectx
aws eks update-kubeconfig --region ap-northeast-2 --name broadpeak-stg-cluster --profile admin-perm-257064912916 --alias broadpeak-stg-cluster

## lens
lens

## terraformer

```
brew install terraformer
curl -LO https://releases.hashicorp.com/terraform-provider-aws/4.22.0/terraform-provider-aws_4.22.0_darwin_amd64.zip
curl -LO https://releases.hashicorp.com/terraform-provider-google/4.28.0/terraform-provider-google_4.28.0_darwin_amd64.zip
curl -LO https://releases.hashicorp.com/terraform-provider-azurerm/3.13.0/terraform-provider-azurerm_3.13.0_darwin_amd64.zip


mkdir -p ~/.terraform.d/plugins/darwin_arm64
mv *.zip ~/.terraform.d/plugins/darwin_arm64
cd ~/.terraform.d/plugins/darwin_arm64

unzip terraform-provider-aws_4.22.0_darwin_amd64.zip
unzip terraform-provider-azurerm_3.13.0_darwin_amd64.zip
unzip terraform-provider-google_4.28.0_darwin_amd64.zip

rm -rf *.zip
aws sso login --profile=admin-perm-025713016147
terraformer import aws -r eks --regions=ap-northeast-2 --profile=admin-perm-025713016147

```

## textmate 

```
# textmate 쉘에서 실행하기 
mate .
```

## linux
xfs
inode
lsblk

```
$ for i in {1..3}; do dig www.chequer.io +short; done | sort | uniq -c | sort -nr
   3 chequer-redirect-lb-341881901.ap-northeast-2.elb.amazonaws.com.
   3 52.78.243.87
   3 13.124.113.162

$ watch -d -n 3 'dig www.chquer.io +short'
$ watch -d -n 3 'netstat -an | grep ESTABLISHED'   
```

```
$ du -d 1 -h development
```

## aws
AWS IAM과 친해지기 (레벨 200) – 조이정:: AWS Builders Online Series
Enable IAM Roles for Service Accounts (IRSA) on the EKS cluster

## terraform
Modules - https://velog.io/@gentledev10/terraform-modules
data
Data Source: aws_eks_cluster_auth
Kubernetes Provider
tags
terraform-aws-modules/terraform-aws-security-group
merge

```
terraform apply -auto-approve --var-file terraform.tfvars
```

# eks
EKS에서 쿠버네티스 포드의 IAM 권한 제어하기: Pod Identity Webhook

```
kubectl config view --raw --minify --flatten -o jsonpath='{.clusters[].cluster.certificate-authority-data}' | base64 --decode
```

# docker
```
# tar 파일로 내보내기
docker save -o querypie-api-$VERSION.tar dockerpie.querypie.com/chequer.io/querypie-api:$VERSION

# querypie 이미지 불러오기
docker load -i querypie-app-$VERSION.tar
```

# querypie

- 쿼리 snapshot

---

```
# file open 확인 및 커널 설정 (https://bit.ly/3z13JnE)
sysctl -a | grep fs.file-max
fs.file-max = 65536g
```

# kubernetes

```
--dry-run=client flag to preview the object that would be sent to your cluster, without really submitting it..
```

# 2022-08-01 
## Sync-Up Meeting

- DAST
- 내부 API가 있는가? (Swagger API Test)
  - https://sandbox.querypie.com/api/docs
- 디벨로퍼 애드보킷 서적 연구 (디벨로퍼 애드보킷 테크 에반젤리스트로 산다는 것, 히예르탄 빌렌가 저)
- 인프라
  - 그린랩스
  - 두나무 장애
    - 쿼리 로깅 : QueryCaptor
  - 힐링페이터
  - 플레이그라우든
  - 고객 대응에 집중 
  - NHN 커머스 : 온프렘 K8S 설치 (인그레이스 이슈)
- Business continuity management systems
- Biz Model
  - QueryPie + Command Pie ARR(Annual Recurring Revenue)
  - Security Consulting
  - Zero Trust Security Solution Reselling
- 카카오 인베스트먼트 --> 네이버
- 네이버 클라우드 현장 지원  


## Cloud Grid 미팅
- API로 소프트웨어를 핸들링할 수 있어야 함 
- NoSql 지원
- 도커/쿠버네티스 지원 (클라우드 배포 용이, 확장 가능해야 함)
- 오라클 기반 금융권은 제외 --> 클라우드 타겟
- 기능 비교 우위를 지향하지 않음 --> 웹기반 (New 환경/패러다임) 
- 보안관련 인증 획득
- 신한금융쪽 NoSql 대상
- 공공 (카카오/네이버/KT 클라우드와 함께)

## 온보딩
- 슬랙 모바일
- GOWID 모바일 (노션 참조)
- 개발 도서/강의 - 플렉스 워크플로우 --> 먼데이
- 카카오티 비즈니스 1116

## Certified Kubernetes Administrator (CKA) with Practice Tests
- https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/


# 2022-08-02

- `디벨로퍼 애드보킷 테크 에반젤리스트로 산다는 것`
- AWS 플레이그라운드
- NHN커머스 `On-Prem K8S Ingress 이슈`

## Sales Lab
- Monday Demo Request: https://chequer.monday.com/boards/2698171653
- 신한카드 Hive, Impala 관련 미팅 (8/4)
- 미리다 POC설치 (8/4)
- 리맴버 데모/시연 (8/10)
- 네이버 클라우드 POC설치 (8/11)

## terminology
- Zero Trust
- Business Continuity Management
- ARR(Annual Recurring Revenue)
- 포커스 파트너

## Certified Kubernetes Administrator (CKA) with Practice Tests
- https://bit.ly/3vzBg7v

## QueryPie API (0.9)
- https://sandbox.querypie.com/api/docs

## Monday.com Personal Info. Masking
- 먼데이닷컴에 `Security Masking 앱`을 만듬
- 보안팀에서 자동화하는 건 대부분 추적 이력관리를 위해 `Okta Workflows` 사용
  -  https://www.okta.com/platform/workflows/








