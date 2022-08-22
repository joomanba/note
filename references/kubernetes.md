## AWS Profile

1. `~/.aws/credentials` - 프로파일 추가
2. `aws configure list-profiles` 전체 프로파일 확인
3. `export AWS_PROFILE=[PROFILE_NAME]` 프로파일 설정
4. `aws configure list` 현재 프로파일 확인

## Kubernetes Cluster 변경 

### kubectx
https://github.com/ahmetb/kubectx

```
$ kubectx infrastructure-production=arn:aws:eks:ap-northeast-2:789971138545:cluster/infrastructure-production

$ kubectx infrastructure-production
Switched to context "infrastructure-production".

$ kubectx -                        
Switched to context "infrastructure-dev".
```
## 워크 노드 업그레이드 
```bash
# 워커 노드 스케줄링 비활성화
$ k get no -l instancegroup=workers-v1 | grep -v 'NAME' | awk '{PRINT $1}' | xargs -I {} kubectl cordon {}

# 워커 노드 Pod Drain 
$ k get no -l instancegroup=workers-v1 | grep -v 'NAME' | awk '{PRINT $1}' | xargs -I {} kubectl drain --delete--emptydir-data --ignore-daemonsets --skip-wait-for-delete-timeout-0 {}
```

## Security
### EKS에서 쿠버네티스 포드의 IAM 권한 제어하기: Pod Identity Webhook

```
kubectl config view --raw --minify --flatten -o jsonpath='{.clusters[].cluster.certificate-authority-data}' | base64 --decode
```

## tips
```
--dry-run=client flag to preview the object that would be sent to your cluster, without really submitting it..
```

## Kubernetes in action
### Chapter 09

```
readinessProbe(x) --> minReadySeconds: 10
rollout(1/1) --> maxSurge: 1
```

### Chapter 10 StatefulSets

