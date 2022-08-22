# EKS 교육
- ENV : dashboard.eventengine.run
- Account : ejjang@gsretail.com	f6e3-02185143f4-39	
- Session Materials : https://cutt.ly/hG43zoO
- Workshop : bit.ly/3IcPcbU
- https://www.eksworkshop.com/

## Docker 
- chroot
```
docker inspect
- GraphDriver
  - mergedDir
/var/lib/docker/overlay2/8488ccfa76bc5cfbfd844dbe4870b1e2cad2b68dd1842b8e6c17dcf6cb290e7b/merged
```

- PID namespace
```
apt-get update && apt-get install procps
ps -ef 
- 1번 PID를 달고 컨테이너를 실행

root@4122b08202fe:/# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 01:05 ?        00:00:00 nginx: master process nginx -g daemon off;


```

- iptables
```
# docker0

docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:68ff:fed0:bd5a  prefixlen 64  scopeid 0x20<link>
        ether 02:42:68:d0:bd:5a  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 39  bytes 6536 (6.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- 파일시스템
```
overlay2, aufs, ...


        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/8488ccfa76bc5cfbfd844dbe4870b1e2cad2b68dd1842b8e6c17dcf6cb290e7b-init/diff:/var/lib/docker/overlay2/1b96ecf00ba019471d298cf0084805e74d716cc0d8784a99ec5e2bf07eeeaab9/diff:/var/lib/docker/overlay2/d4c90d78258c5e62838e2304386f86c4e5c845adc9f44b453274372f7d54c98c/diff:/var/lib/docker/overlay2/60b40cd710ff43460254c5a8d76d1f120c0c541a96282e414f069ca7e8c8a761/diff:/var/lib/docker/overlay2/6b974c958c7ffec53513fb267b738f738383669a28ff0a8245d757653b2beaf2/diff:/var/lib/docker/overlay2/3b6c918d195b04653b1374744bf01981eb522b3e92612abd1db387724bcc3d82/diff:/var/lib/docker/overlay2/86f742228c133e273f6bd7351eb4ede78b2ec7c7c852c921c094c5d0690da652/diff",
                "MergedDir": "/var/lib/docker/overlay2/8488ccfa76bc5cfbfd844dbe4870b1e2cad2b68dd1842b8e6c17dcf6cb290e7b/merged",
                "UpperDir": "/var/lib/docker/overlay2/8488ccfa76bc5cfbfd844dbe4870b1e2cad2b68dd1842b8e6c17dcf6cb290e7b/diff",
                "WorkDir": "/var/lib/docker/overlay2/8488ccfa76bc5cfbfd844dbe4870b1e2cad2b68dd1842b8e6c17dcf6cb290e7b/work"
            },
            "Name": "overlay2"

```

- layer
```
RUN 명령이 하나의 레이어
변경이 자주 되는 것은 최대한 하위에 있어야 한다.
```

- network
```
root@euijin-900X5T:~# iptables -t nat -S
...
-A POSTROUTING -m comment --comment "kubernetes postrouting rules" -j KUBE-POSTROUTING
-A POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE
-A POSTROUTING -s 192.168.49.0/24 ! -o br-59b0ae9ca515 -j MASQUERADE
...

docker run -dit -p 80:80 ngix

root@euijin-900X5T:~# iptables -t nat -S
...
-A POSTROUTING -s 172.17.0.2/32 -d 172.17.0.2/32 -p tcp -m tcp --dport 80 -j MASQUERADE

```

- Fine Bin Packing 
  - [확장된 리소스를 위한 리소스 빈 패킹(bin packing)](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/resource-bin-packing/)

## Kubernetes

- containerd 
- schedulter : affinity, taint, ...

### kubernetes network

- Overlay Network
  - AWS CNI : 실제 VPC IP를 받음 

### kubernetes Object

- Volume
  - emptyDir, hostPath, Persistent Volume
  
- Deployment
  - Rolling Update
  - ReplicaSet
  
- Service
  - endpoint 
  - FQDN: myapp.default.svc.cluster.local --> endpoint
  
## EKS

- managed node group
- Fargate와 섞어서 사용하는 것이 좋음
  - 비용과 부팅 속도 이슈 있음 

- 전략적 운영 + 전술적 운영

- 인스턴스 모드 보다는 IP모드 사용 추천