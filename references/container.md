### docker 
```
# tar 파일로 내보내기
docker save -o querypie-api-$VERSION.tar dockerpie.querypie.com/chequer.io/querypie-api:$VERSION

# querypie 이미지 불러오기
docker load -i querypie-app-$VERSION.tar
```

### Tips
- [Docker prune 사용법: 사용하지 않는 Docker 오브젝트 일괄 삭제](https://www.lainyzine.com/ko/article/docker-prune-usage-remove-unused-docker-objects/)

```
# 중지된 지 1시간 이상 지난 이미지만 삭제
$ docker container prune --filter until=1h

# env 키가 없는 이미지
$ docker container prune --filter label!=env

$ docker image prune
$ docker volume prune
$ docker network prune

# 사용하지 않는 Docker 오브젝트 전부 삭제
$ docker system prune --volumes
```