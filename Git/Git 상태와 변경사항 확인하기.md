<img src="https://ifh.cc/g/hM2RJd.png" style="max-width: 100%" align="center">

### 목차
- [1 상태와 변경사항](#1-상태와-변경사항)
  - [1-1 상태 확인하기](#1-1-상태-확인하기)
  - [1-2 변경사항 확인하기](#1-2-변경사항-확인하기)
- [2 커밋 내역 살펴보기](#2-커밋-내역-살펴보기)
  - [2-1 git log](#2-1-git-log)
  - [2-2 git log 옵션](#2-2-git-log-)

***

<br>

## 1 상태와 변경사항

### 1-1 상태 확인하기
바로 [앞서](#4-2-로컬-저장소에-커밋하기) `status` 명령어를 살펴보았다. 특정 작업 파일이 Git에 버전 관리를 위해 인식이 된 상태인지 아닌지 확인할 수 있다

```bash
git status

```

`Untracked files`는 스테이지에 올린적이 없어(`git add`하지 않은) Git 버전 관리가 되지 않는 파일을 의미한다. `Changes to be committed`는 스테이지에 올라가 커밋될 준비가 된 상태를 의미한다

### 1-2 변경사항 확인하기
`diff` 명령어는 작업 파일을 스테이지에 올리기 전 최근 커밋한 내용과 작업 중이었던 파일의 변경사항을 출력한다. `diff`는 'difference'의 약자다. 앞서 생성한 `README.md` 파일에 내용을 임의로 추가하고 `diff` 명령어를 실행 시 아래와 같은 출력 결과를 볼 수 있다

```bash
git diff

→ diff --git a/README.md b/README.md
  index e69de29..e30cf0b 100644
  --- a/README.md
  +++ b/README.md
  @@ -0,0 +1,3 @@
  +이 파일의 내용을 지금 추가해봤습니다. 
  +
  +diff 명령어로 전후 상태를 비교해보세요.
```

<br>

## 2 커밋 내역 살펴보기

### 2-1 git log
`log` 명령어는 최근 커밋한 내역을 출력해주기 때문에 커밋 사항들을 조회할 수 있다. 

```bash
git log

→ commit 8d608f67fac92c63d1cdcbb4bf827a6ba960c9ab (HEAD -> main)
  Author: jacenam <jace.nams@gmail.com>
  Date:   Wed Mar 15 19:22:46 2023 +0900

    README.md 파일에 내용 추가

  commit 3fcc857803e44458c545933d49e1deb4ca17b1ff
  Author: jacenam <jace.nams@gmail.com>
  Date:   Wed Mar 15 14:57:59 2023 +0900

    first version
```
조회한 내역을 토대로 이전 커밋했던 상태로 파일의 버전을 되돌릴 수도 있다. `git checkout 커밋 ID 앞 7자리`의 형태로 원하는 버전의 ID를 입력해 파일을 되돌리는 것이다

```bash
git checkout 3fcc857

→  HEAD is now at 3fcc857 first version
```

### 2-2 git log 옵션
`git log`는 사용자의 커밋 ID, 작성자(Author), 커밋 시간(Date), 커밋 내용(Commit Message) 등의 내용을 출력한다. 이때 커밋했던 내역이 보여지는 형태를 여러가지 옵션으로 지정할 수 있다. 대표적인 옵션들만 추리자면 아래 내용과 같다: 

-  `git log --pretty`: 간단명료하게 커밋 이력을 요약해서 출력한다
    
    ```bash
    git log --pretty
    
    → commit 3fcc857803e44458c545933d49e1deb4ca17b1ff (HEAD) | Author: jacenam <jace.nams@gmail.com>
    ```
- `git log --oneline`: 커밋 이력을 한 줄로 요약해서 출력한다
    
    ```bash
    git log --oneline
    
    → 3fcc857 (HEAD) first version
    ```
- `git log --graph`: 커밋 이력을 그래프로 나타낸다. 여러 브랜치가 존재할 때 유용한 옵션이다. 위 옵션들과 결합해서 `git log --graph --pretty=oneline`처럼 동시에 사용하면 더 편리하게 간결한 커밋 이력을 확인할 수 있다

> 이 외에도 `short`, `medium`, `full`, `fuller`, `reference`, `email` 등 다양한 옵션들이 있다. 모두 암기할 필요는 없으니 필요에 따라 [Git 공식문서](https://git-scm.com/docs/pretty-formats)를 참고하자

<br>

***

### 참고
- [Git 공식문서](https://git-scm.com/docs)
- [팀 개발을 위한 Git, Github 시작하기](http://www.yes24.com/Product/Goods/85382769)
- [알아서 잘 딱 깔끔하고 센스있게 정리하는 Github 핵심 개념](https://m.yes24.com/Goods/Detail/108203273)
