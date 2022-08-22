# Branch 생성 방식과 네이밍 규칙



https://velog.io/@kim-jaemin420/Git-branch-naming

[ANTI-PATTERN 사례 - annapurna](https://git.gsenext.com/himalaya/annapurna)

## Bracnh의 종류
### Main branch

**master branch**

제품으로 출시될 수 있는 브랜치

![master branch](./images/git-01.png)


**develop branch**

다음 출시 버전을 개발하는 브랜치 


### Supporting branches 

**feature branch**

기능을 개발하는 브랜치

> 더 이상 필요하지 않은 feature 브랜치는 삭제한다.

```shell
# develop 브랜치에서 생성
git checkout -b feature/login develop
```

**relase branch**

이번 출시 버전을 준비하는 브랜치

![releash branch](./images/git-02.png)

**hotfix branch**

출시 버전에서 발생한 버그를 수정하는 브랜치

![releash branch](./images/git-03.png)


## branch 네이밍 규칙

- `feature/기능요약` 형식을 추천한다. ex) feature/login
- `feature/{issue-number}-{feature-name}` 이슈추적을 사용한다면 이와 같은 형식을 따른다.
  - ex) feature/1-init-project, feature/2-build-gradle-script-write
- `release-RB_...` 또는 `release-...` 또는 `release/...` 같은 이름이 일반적이다.
  - ex) release-1.2
-  `hotfix-...` 형식을 추천한다. ex) hotfix-1.2.1


## branch 관련 명령

**원격 저장소 branch 가져오기**
```
git remote update

# 원격 저장소 branch 확인
git branch -r 

# 원격/로컬 저장소 모든 branch 확인
git branch -a

# 원격 저장소의 branch 가져오기 (원격저장소 브랜치이름과 동일하게)
git checkout -t origin/development

# 원격 저장소의 branch 가져오기 (브랜치 이름 변경)
git checkout -b [생성할 branch 이름] [원격 저장소의 branch 이름]
```