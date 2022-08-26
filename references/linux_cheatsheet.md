## 리눅스 Cheat Sheet

#### Public IP (NAT IP) 확인
```
curl ifconfig.me
```

#### sed

```shell
# 참조 : https://bit.ly/3ASWxJk

sed -e "s/IMAGE_TAG_HOLDER/$(echo $DOCKER_IMAGE_TAG | sed 's/\//\\\//g'):$CI_PIPELINE_ID/g" ./deploy/deploymemt.yml > deploy.yml
```

```shell
$ echo "/tmp/test/folder1/test.txt" | sed 's/\//\\\//g' 
\/tmp\/test\/folder1\/test.txt
```

```shell
# '  annotations:' 포함하는 행과 다음 2개의 행을 삭제
$ sed '/^  annotations:/,+2 d' > aws-auth.yaml
```

```shell
# hello.txt 파일내 모든 `YOUR_NAME`을 변경
$ sed -i "s/YOUR_NAME/${YOUR_NAME}/g" hello.txt 
```

#### tar

```shell
$ tar -cvf promotion-ui.tar * --exclude="*.pem"
```

#### grep

```
# creationTimestamp, resourceVersion, selfLink, uid 포함하는 행을 제외하고 선택
$ kubectl get configmap -n kube-system aws-auth -o yaml | grep -v "creationTimestamp\|resourceVersion\|selfLink\|uid"
```

**greb -An**
단어가 들어가는 row 포함 아래 n줄을 같이 출력
```
kubectl describe pod elephant | grep -A5 State
```


#### dig

```
$ for i in {1..3}; do dig www.chequer.io +short; done | sort | uniq -c | sort -nr
   3 chequer-redirect-lb-341881901.ap-northeast-2.elb.amazonaws.com.
   3 52.78.243.87
   3 13.124.113.162

$ watch -d -n 3 'dig www.chquer.io +short'
$ watch -d -n 3 'netstat -an | grep ESTABLISHED'  
```

#### du

```
$ du -d 1 -h development
```

#### pushd & popd
디렉토리를 이동하면서 스택(Stack)에 작업 디렉토리를 기억
```
$ pushd /tmp
$ pushd /etc
$ popd
$ popd
```

#### untill
```bash
# sh -c "until nslookup mydb; do echo waiting for mydb; sleep 2; done;"
```

