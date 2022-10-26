## cheat sheet

https://ryanstutorials.net/linuxtutorial/cheatsheetvi.php

### Basic Usage
### Search and Replace
### Movement

```
w    : 단어 첫 글자 기준으로 다음으로 이동
W    : 공백 기준으로 다음으로 이동
b    : 단어 첫 글자 기준으로 이전으로 이동
B    : 공백 기준으로 이전으로 이동
gg   : 문서 맨 앞으로 이동
G    : 문서 맨 뒤로 이동
^    : 문장 맨 앞으로 이동
$    : 문장 맨 뒤로 이동
:숫자 : 지정한 줄 번호로 이동

Ctrl + f : 다음 페이지로 이동
Ctrl + b : 이전 페이지로 이동
```
### Deleting
### Cut and Paste
### Entering Insert mode


https://kwmblog.tistory.com/11
```
i : 입력모드로 변경한다.
Shift + a : 해당 라인 끝으로 이동하면서 입력모드로 변경한다.

u : 작업취소

x  : 커서가 있는 곳의 문자를 삭제
dw : 한 단어를 삭제
d$ : 커서 이후로 줄 끝까지 삭제
dd : 커서가 있는 한줄 삭제
dG : 커서 이후로 모두 삭제

set number : 문서에 라인 숫자를 출력한다. (set nu 라고 입력해도 나온다)

yy : 커서가 있는 한 줄을 복사한다.
p : 복사한 줄을 붙여넣기 한다.

/키워드 : 문서 내 키워드를 검색한다.

:%s/원본문자/바꿀문자/g : 원본문자를 바꿀문자로 치환한다.

여러줄 한번에 들여 쓰기: Shift + B, Shift + >
```

### history

```
ctrl + r

# !숫자
# !명령어
```

### References
* [vi editor 문자열 치환(replace) 방법](https://techlog.gurucat.net/297)
* [히스토리 명령어 옵션 및 기능](https://coconuts.tistory.com/m/781)
