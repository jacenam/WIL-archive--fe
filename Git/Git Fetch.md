<img src="https://ifh.cc/g/xmybLo.png" style="max-width: 100%" align="center">

### 목차

- [1 Git Fetch](#1-Git-Fetch)
- [2 FETCH_HEAD](#2-FETCH_HEAD)
- [3 fetch와 pull의 차이](#3-fetch와-pull의-차이)
- [4 Git Fetch의 활용](#4-Git-Fetch의-활용)

***

<br>

## 1 Git Fetch

`git fetch [원격 저장소 이름]` 명령어는 대상 원격 저장소의 변경된 이력만을 로컬 저장소로 가져와서 변경 정보만을 확인시켜주는 기능이 있다. 보통  `clone` 혹은 `remote add`를 통해 원격 저장소와 로컬 저장소를 연결시킨 상태에서 원격 저장소에 변경된 이력이 있다면, 로컬 저장소로 그 변경 사항을 전부 가져오기 전에 먼저 변경 이력을 확인할 때 사용된다 

`git-practice`라는 이름의 원격 저장소에 아무런 변경 사항이 없는 `README.md` 파일이 있다고 가정하자. 로컬 저장소로 원격 저장소를 `clone`하여 동일한 상태로 동기화했다

```bash
git clone https://github.com/jacenam/git-practice.git .

→ Cloning into '.'...
  remote: Enumerating objects: 3, done.
  remote: Counting objects: 100% (3/3), done.
  remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
  Receiving objects: 100% (3/3), done.
```

만약 원격 저장소의 `README.md` 파일의 변경(커밋) 이력이 생겼다면 `fetch` 명령어를 통해 아래와 같이 변경 정보를 확인할 수 있다:

> `git fetch [원격 저장소 이름] ` 형태의 명령어에서 원격 저장소 이름은 `origin`이다

```bash
git remote -v 

→ origin	https://github.com/jacenam/git-practice.git (fetch)
  origin	https://github.com/jacenam/git-practice.git (push)
  
git fetch origin

→ remote: Enumerating objects: 5, done.
  remote: Counting objects: 100% (5/5), done.
  remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
  Unpacking objects: 100% (3/3), 655 bytes | 655.00 KiB/s, done.
  From https://github.com/jacenam/git-practice
    0416553..0b5345e  main       -> origin/main 
```

`fetch`가 완료되면 `log` 명령어를 통해 원격 저장소에서 로컬 저장소로 가져온 변경 정보를 확인할 수 있다(변경 사항, 즉 커밋 이력을 로컬 저장소에 적용하는 `pull`이 아니라는 것에 주의하자)

```bash
git log --all --oneline

→ 0b5345e (origin/main, origin/HEAD) Docs: have added content at README.md
  0416553 (HEAD -> main) Initial commit
```

### <br>

## 2 FETCH_HEAD

위 로그를 살펴보면 `0b5345e` 커밋 ID는 `(origin/main, origin/HEAD)`, `0416553` 커밋 ID는 `(HEAD -> main)`으로 되어있다. 이는 원격 저장소에서 로컬 저장소로 커밋 정보만 가져왔음을 나타낸다. 즉, 로컬 저장소의 `HEAD`는 `0416553` 커밋을 가리키고 있고, `fetch`해온 원격 저장소의  `HEAD`는 `0b5345e` 커밋을 가리키고 있는 것이다. 이때 `fetch`해온 원격 저장소의 `HEAD`를 `FETCH_HEAD`라고 부른다

<img src="https://ifh.cc/g/DfZb0V.png" style="max-width: 100%" align="center">

실제로 로컬 저장소의 `.git` 디렉토리를 살펴보면 원격 저장소의  `0b5345e` 커밋 내역을 `fetch`해온 정보가 파일에 저장되어 있음을 확인할 수 있다

<img src="https://ifh.cc/g/SSDVBy.png" style="max-width: 100%" align="center">

`fetch` 명령어를 통해 가져온 원격 저장소의 커밋 정보를 `FETCH_HEAD`를 통해 로컬 저장소에 동기화시킬 수 있다. 이는 `merge` 명령어를 통해 로컬 저장소를 가져온 최신 커밋대로 업데이트시킬 수 있다

```bash
git merge FETCH_HEAD

→ Updating 0416553..0b5345e
  Fast-forward
   README.md | 3 ++-
   1 file changed, 2 insertions(+), 1 deletion(-)
```

`log` 명령어를 통해 원격 저장소의 최신 커밋 이력이 모두 로컬 저장소로 적용되었음을 확인할 수 있다. 로컬 저장소의  `HEAD`가 최신 커밋인 `0b5345e`로 변경되었다

```bash
git log --all --oneline

→ 0b5345e (HEAD -> main, origin/main, origin/HEAD) Docs: have added content at README.md
  0416553 Initial commit
```

<br>

## 3 fetch와 pull의 차이

앞서 `fetch` 명령어는 원격 저장소의 최신 커밋 이력이 로컬 저장소에 적용되지 않았을 때 로컬 저장소로 먼저 커밋 정보만 가져오는 기능을 수행한다 했다. 그에 반해, `pull` 명령어는 원격 저장소의 최신 커밋 이력을 로컬 저장소로 가져와 커밋 이력을 동기화한다. 이를 세부 과정으로 나타냈을 때: 

- `fetch`: 원격 저장소 → `fetch` → 로컬 저장소 → `merge FETCH_HEAD` → 동기화 완료
- `pull`: 원격 저장소 → `pull` → 로컬 저장소(동기화 완료)

즉, `pull` 명령어는 `fetch` + `merge` 명령어들을 한번에 수행해주는 명령어다. 따라서 원격 저장소와 로컬 저장소 간의 버전 관리 차이가 있다고 우려되거나 확실치 않을 시 `fetch` 명령어를 통해 먼저 원격 저장소의 최신 커밋 정보를 살펴보고, 그 이후 `pull` 혹은 `merge` 명령어를 통해 두 저장소 간의 동기화를 실행하는 것이 안전하다

<br>

## 4 Git Fetch의 활용

현재 [코드스쿼드 마스터즈](https://codesquad.kr/masters)에서 활동을 하며 `git fetch` 명령어를 활용해 볼 기회가 있어 내용을 정리한다

2인 1조로 미션을 수행하는데 1명만이 Pull Request를 코드스쿼드 원본 원격 저장소로 날리면 되는 상황이다. 따라서 각자 작업을 하며 서로의 원격 저장소로부터 작업 상태를 동기화한 뒤 PR를 원본 원격 저장소로 날리는 방식을 선택했다

<img src="https://ifh.cc/g/OMm789.jpg" style="max-width: 100%" align="center" />

1. 코드스쿼드의 원본 원격 저장소를 각자의 원격 저장소로 Fork했다([Fork하는 방법](https://github.com/jacenam/WIL-archive/blob/main/Git/Git%20Fork.md#3-Fork하는-방법))

2. 각자의 원격 저장소에서 각자 작업할 브랜치를 생성했다(각자의 이름으로 브랜치를 생성했다)

   ```bash
   git switch -c jace
   ```

3. 그 다음 upstream을 각자의 원격 저장소 브랜치로 설정했다(편의상 상대방의 Github 닉네임 및 브랜치명은 `mypeer`로 하겠다)

   > `git remote add -t [branch name] upstream [branch URL]` 형태로 각자 원격 저장소를 upstream 설정했다. 여기서 `-t`는 'track'을 의미한다. `git remote add [repository name]`의 형태로 명령어를 입력하면 해당 원격 저장소를 나의 로컬 저장소와 연결시키지만, 해당 원격 저장소의 특정 브랜치만을 선택할 수 없다. 따라서 `-t` 명령어로 상대방 원격 저장소의 특정 브랜치를 지정해서 변경 정보를 추적하도록 했다. `upstream`에 대한 내용은 [여기](https://github.com/jacenam/WIL-archive/blob/main/Git/Git%20Fork.md#4-fork를-위한-remote와-upstream에-대해-이해하기)를 참고하자

   ```bash
   git remote add -t mypeer upstream https://github.com/mypeer/fe-max--mailbox.git
   
   git remote -v 
   
   → origin	https://github.com/jacenam/fe-max--mailbox.git (fetch)
     origin	https://github.com/jacenam/fe-max--mailbox.git (push)
     upstream	https://github.com/mypeer/fe-max--mailbox.git (fetch)
     upstream	https://github.com/mypeer/fe-max--mailbox.git (push)
   ```

4. 이후 각자 작업을 하며 서로의 작업 사항을 동기화할 때는 

   1. 먼저 각자 작업에 대한 커밋을 각자 원격 저장소로 `push`해서 원격 저장소와 로컬 저장소를 동기화해놓고

   2.  `fetch` 명령어를 활용했다. `fetch` 명령어를 통해 상대방 브랜치의 커밋 정보를 가져왔다

      ```bash
      git fetch upstream mypeer
      ```

5. 그리고 `rebase` merge를 통해 각자 작업에서의 충돌을 해결하고 각자의 작업을 하나의 통일된 작업 형태로 만들었다. 이때 `rebase`를 활용했기 때문에 각자 작업의 커밋 이력을 모두 유지한 채 merge를 진행했다

   ```bash
   git rebase upstream/mypeer (내가 상대방의 작업을 rebase & merge할 때)
   git rebase upstream/jace (상대방이 나의 작업을 rebase & merge할 때)
   
   → Successfully rebased and updated refs/heads/jace
   ```

   예를 들어, 상대방의 작업 브랜치인 `mypeer`에 나의 작업 브랜치인 `jace`의 커밋 이력을 `rebase`한다 가정했을 때 아래 그림과 같이 표현할 수 있다:

   <img src="https://ifh.cc/g/SWWs0V.jpg" style="max-width: 100%" align="center">

   충돌 내용을 모두 수정한 후 rebase & merge를 했을 때의 작업물 병합 상태는 아래 그림과 같이 표현할 수 있다. 그리고 상대방이 나의 커밋 이력을 자신의 `mypeer` 브랜치에 병합하여 병합 사항을 최종적으로 본인의 원격 저장소에 `push`하고, 나의 로컬 저장소에 상대방의 원격 저장소 변경 이력을 `fetch`해온 후 동일한 과정으로 `rebase` 및 병합한다. 마지막으로 나의 원격 저장소에 병합 사항을 `push`해주면 상대방과 나의 작업물이 동일하게 동기화된다

   <img src="https://ifh.cc/g/V3hN2g.jpg" style="max-width: 100%" align="center">

<br>

***

### 참고

- [Git 공식문서](https://git-scm.com/docs)
- [팀 개발을 위한 Git, Github 시작하기](http://www.yes24.com/Product/Goods/85382769)
- [알아서 잘 딱 깔끔하고 센스있게 정리하는 Github 핵심 개념](https://m.yes24.com/Goods/Detail/108203273)
- [Git Fetch 커밋 정보 가져오기](https://velog.io/@devp1023/GIT-Fetch)
- [Fetch, 원격 저장소의 데이터를 로컬에 가져오기만 하기](https://backlog.com/git-tutorial/kr/stepup/stepup3_2.html)

