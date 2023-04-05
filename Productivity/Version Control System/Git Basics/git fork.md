<img src="https://ifh.cc/g/ffcGp7.png" style="max-width: 100%" align="center">

### 목차

- [1 Fork란]()
- [2 Fork와 Branch의 차이](#2-Fork와-Branch의-차이)
  - [2-1 Branch의 분기 특성](#2-1-Branch의-분기-특성)
  - [2-2 Fork의 분기 특성](#2-2-Fork의-분기-특성)
- [3 Fork하는 방법](#3-Fork하는-방법)
- [4 Fork를 위한 remote와 upstream에 대해 이해하기](#4-Fork를-위한-remote와-upstream에-대해-이해하기)
  - [4-1 remote](#4-1-remote)
  - [4-2 upstream](#4-2-upstream)
  - [4-3 Fork와 remote, upstream](#4-3-Fork와-remote,-upstream)

***

<br>

## 1 Fork란

일반적으로 저장소를 생성한 사람만이 생성한 원본 저장소에 커밋 내역을 업데이트(`push`)할 권한이 있다. 권한 등록이 되지 않은 다른 사람이 이곳에 커밋 내역을 업데이트하면 에러가 발생한다. 이때 1) 권한 등록 없이 다른 사람이 원본 저장소를 복사(`Fork`)해 변경 사항을 원본 저장소에 2) 허락(`Pull Request`)을 받고 업데이트할 수 있다

<img src="https://ifh.cc/g/DMfwOB.png" style="max-width: 100%" align="center">

그렇다면 Fork 기능은 왜 필요한 걸까? 이는 원본 저장소에 업데이트 권한을 가진 사람이 많으면 많을수록 원본 저장소의 관리가 어렵고 훼손될 위험성이 높아지기 때문이다. Fork 기능의 대표적인 활용 사례는 아래와 같다: 

- 오픈소스 프로젝트에 A가 수정 혹은 추가 사항을 찾아 기여하는 것
  - 실제로 카카오, 네이버 등의 기업에서 사용하는 오픈소스 코드에서 오류 혹은 기능 개선 사안을 찾아 기여할 수도 있다
- 특정 회사의 입사 과제로 회사의 기능 일부를 B가 Fork한 뒤 과제 수행 후 기능의 원본 저장소로 PR 보내는 것
- 개발자 부트캠프에서 수 많은 수강생들의 작업물을 안전하고 편리하게 관리하기 위해 버전 관리 용도로 사용하는 것

<br>

## 2 Fork와 Branch의 차이

Fork와 Branch는 기존 커밋 내역에서 새로운 분기점을 생성해서 기존 커밋 내역과는 별개의 작업이 가능하다는 공통점이 있지만 특성이 다르다

### 2-1 Branch의 분기 특성

브랜치는 새로운 저장소를 생성하는게 아닌, 기존 작업 브랜치의 `base`를 기준으로부터 새로운 브랜치가 분기되기 때문에 모든 커밋 이력이 연결되어 있다. 따라서 원본 저장소(`main` 브랜치와 `feature` 브랜치)의 커밋 이력에 모두 접근이 가능하다

<img src="https://ifh.cc/g/nDTlYK.png" style="max-width: 100%" align="center">

### 2-2 Fork의 분기 특성

반면, Fork는 브랜치와는 원본 저장소를 통째로 복사해서 아예 다른 저장소에 저장하는 것이다. 이때 원본 저장소의 주소와 Fork해서 새로 생성한 저장소의 주소가 아래와 같이 다르다. 따라서 원본 저장소와 Fork로 생성한 저장소는 연결이 끊겨있기 때문에 새롭게 생성한 저장소에서는 원본 저장소의 커밋 이력을 볼 수 없다. 즉, Fork로 생성한 저장소에서 변경 이력이 발생해도 원본 저장소에는 어떠한 영향을 끼치지 않는 점도 이 때문이다

```bash
원본 저장소 주소: github.com/codesquad/fe-max--wise-wallet
Fork로 생성한 저장소 주소: github.com/jacenam/fe-max--wise-wallet
```

<img src="https://ifh.cc/g/t9cqzo.jpg" style="max-width: 100%" align="center">

정리하자면: 

| 분류 | Fork                                                         | Branch                                        |
| ---- | ------------------------------------------------------------ | --------------------------------------------- |
| 분기 | 하나의 원본 저장소를 여러 원격 저장소로 분기를 나눈다        | 하나의 원본 저장소에서 브랜치로 분기를 나눈다 |
| 커밋 | 원본 저장소의 커밋 이력을 위해 원본 저장소 주소를 추가해야 한다 | 저장소의 전체 커밋 이력에 접근이 쉽다         |
| 관리 | 원본 저장소에 영향을 미치지 않기 때문에 작업이 자유롭다      | 하나의 저장소에서 브랜치별 작업이 자유롭다    |
| 용도 | 다수의 사람이 작업 시 편리하다                               | 소수 인원 작업 시 편리하다                    |

원본 저장소의 커밋 이력과 동일한 커밋 이력을 가져오기 위해서는 추가적으로 원본 저장소의 주소를 등록을 해야 한다. 이 과정에 대해서는 아래에서 살펴볼 예정이다

<br>

## 3 Fork하는 방법

Fork를 하는 방법은 아래와 같다:

1. Fork하고자 하는 Github의 원격 저장소에 들어가서 `Fork` 버튼을 클릭한다

   <img src="https://ifh.cc/g/lX9Nrm.png" style="max-width: 100%" align="center">

2. Fork가 완료되면 나의 Github 원격 저장소 목록에 Fork한 저장소가 생성된다

   <img src="https://ifh.cc/g/KoqsbQ.png" style="max-width: 100%" align="center">

3. Fork한 저장소를 나의 로컬 저장소로 복사(`clone`)한다

   ```bash
   cd [디렉토리 이름]
   git clone [Fork해온 원격 저장소 Git URL] .
   ```

4. (원본 저장소의 커밋 이력 가져오기) 원격 저장소와의 연결 상태를 확인한다. 현재 URL 주소는 나의 원격 저장소를 가리키고 있다 

   ```bash
   git remote -v
   
   → origin https://github.com/jacenam/fe-max--wise-wallet.git (fetch)
     origin https://github.com/jacenam/fe-max--wise-wallet.git (push)
   ```

5. 원본 저장소의 원격 저장소 URL를 추가한다

   > `remote`와 `upstream` 명령어에 대한 자세한 내용은 [아래 내용](#4-Fork를-위한-remote와-upstream에-대해-이해하기)을 참고하자

   ```bash
   git remote add upstream https://github.com/codesquad/fe-max--wise-wallet.git
   ```

6. 원본 저장소의 원격 저장소 URL이 추가되었는지 상태를 확인한다

   ```zsh
   git remote -v
   
   → origin https://github.com/jacenam/fe-max--wise-wallet.git (fetch)
     origin https://github.com/jacenam/fe-max--wise-wallet.git (push)
     upstream https://github.com/codesquad/fe-max--wise-wallet.git (fetch)
     upstream https://github.com/codesquad/fe-max--wise-wallet.git (push)
   ```

7. 원본 저장소의 과거/최신 커밋 내역 정보를 가져온다

   > `fetch` 명령어는 upstream 설정 시 대상의 원본 원격 저장소의 변경 사항을 확인만 하고 로컬 저장소로 변경 데이터를 가져오진 않는다. 만약 upstream을 설정하지 않을 시 `git fetch [나의 원격저장소 이름]`의 형태로 명령어를 입력하여 나의 원격저장소의 최신 정보를 로컬 저장소에서 확인이 가능하다. 이에 대해서는 [Git Fetch]() 파트의 내용을 참고하자

   ```bash
   git fetch upstream
   
   → remote: Enumerating objects: 45, done.
     remote: Counting objects: 100% (45/45), done.
     remote: Compressing objects: 100% (10/10), done.
     remote: Total 25 (delta 19), reused 18 (delta 15), pack-reused 0
     Unpacking objects: 100% (25/25), done.
     From https://github.com/codesquad/fe-max--wise-wallet.git
       5d3b4e6..4f55495  main     -> upstream/main
   ```

8. 내려받은 커밋 이력을 나의 로컬 저장소에 merge 시킨다

   ```bash
   git merge upstream/main
   ```

9. merge가 완료되면 나의 로컬 저장소와 나의 원격 저장소를 동기화한다. 원본 저장소와 나의 원격 저장소의 커밋 이력이 동일하게 맞춰진 것을 확인할 수 있다

   ```bash
   git push origin main
   ```

<br>

## 4 Fork를 위한 remote와 upstream에 대해 이해하기

### 4-1 remote

나의 로컬 저장소를 원격 저장소와 연결하는 방법은 두 가지가 있다: 

1. `git remote add origin [원격 저장소 URL]`
2. `git clone [원격 저장소 URL] .`

위 명령어를 실행한 후 원격 저장소와의 원격 연결 상태를 확인해보면 아래와 같은 결과가 나온다

```bash
git remote -v

→ origin	https://github.com/jacenam/git-practice.git (fetch)
  origin	https://github.com/jacenam/git-practice.git (push)
```

이때 `origin`이라는 결과는 Github에 호스팅되어 있는 나의 원격 저장소를 의미한다. 즉, 원격(Remote)에 있는 저장소를 원천(origin)으로 삼아 원격으로(`remote`) 로컬 저장소와 연결하여 작업물을 주고 받는다는 뜻이다. ***다시 말해, 원격 저장소는 `origin`이라 볼 수 있다***

그렇다면 앞서 [원격 저장소 초기 설정](https://github.com/jacenam/WIL-archive/blob/main/Git/Github%20초기%20설정%20및%20기초%20명령어(feat.%20원격%20저장소).md)에서 다룬  `git remote add origin [원격 저장소 URL]`, `git push -u origin main`, `git pull origin main`과 같은 명령어의 의미도 아래와 같이 해석해 볼 수 있다:

- `git remote add origin [원격 저장소 URL]`: 데이터를 가져오고(`pull`) 보낼(`push`) 원격 저장소(`origin`)를 나의 로컬 저장소로 원격(`remote`)으로 연결(`add`)한다
- `git push -u origin main`: 원격 저장소(`origin`)에 나의 로컬 저장소 데이터를 보낸다(`push`)
- `git pull origin main`: 원격 저장소(`origin`)으로부터 나의 로컬 저장소로 데이터를 가져온다(`pull`)

그렇다면 `git push -u origin main`에서 `-u`는 무엇을 의미하는 것일까?

### 4-2 upstream

위에서 언급한  `git push -u origin main` 명령어에서 `-u`는 `--set-upstream` 명령어와 동일한 의미를 지니며 upstream을 설정한다는 뜻이다. 그렇다면 upstream은 무슨 의미일까? `push`, `pull` 명령어를 살펴보자: 

- `push`: 로컬 저장소에서 원격 저장소(`origin`)로 데이터를 보낸다
- `pull`: 원격 저장소(`origin`)에서 로컬 저장소로 데이터를 가져온다

데이터를 `push` 또는 `pull`할 때 데이터가 흐르는 방향을 `upstream/downstream`으로 아래 그림과 같이 표현할 수 있다: 

<img src="https://ifh.cc/g/o2Yjnc.jpg" style="max-width: 100%" align="center">

`-u` 명령어로 upstream을 설정한다는 것은 커밋 이력이 어디서 어디로 흐르는지 방향을 설정한다는 의미다. 즉, `git push -u origin main` 명령어를 통해 로컬 저장소에서 원격 저장소(`origin`)로 데이터를 보내는 방향은 upstream, 반대로 원격 저장소에서 로컬 저장소의 방향은 downstream으로 설정한다는 뜻이다

### 4-3 Fork와 remote, upstream

여기서 주의해야 할 점은 upstream/downstream 개념은 상대적이라는 것이다. 아래와 같이 다른 사람의 원본 원격 저장소를 나의 원격 저장소로 통째로 복사해오는 Fork의 과정을 살펴보자:

- 원본 원격 저장소 → `fork` → 나의 원격 저장소(`origin`) → `clone` → 나의 로컬 저장소

앞서 나의 원격 저장소와 로컬 저장소만을 고려했을 때는 `origin`인 원격 저장소가 upstream, 로컬 저장소가 downstream이었는데, Fork의 경우는 아래와 같이 upstream/downstream의 관계가 형성된다: 

- 원본 원격 저장소(upstream) → 나의 원격 저장소(`origin`이자 downstream) → `clone` → 나의 로컬 저장소(downstream)

따라서 앞서 언급한 [Fork하는 방법](#3-Fork하는-방법) 중 `git remote add upstream [원본 원격 저장소 URL]` 명령어를 통해 upstream을 설정하고 원본 저장소의 커밋 이력을 `git fetch upstream` 명령어를 통해 로컬 저장소로 가져오는 upstream 명령어 설정이 필요한 이유다. 아래와 같이 상황에 따라 유동적으로 upstream에 변경 사항을 반영할 필요가 있기 때문이다: 

- Fork 이후 원본 원격 저장소에 변경 사항이 없을 경우
  1. 나의 로컬 저장소 작업 사항(커밋 이력) → `push` → 나의 원격 저장소(`origin`)
  2. 나의 원격 저장소(`origin`) → `PR` → 원본 원격 저장소(upstream)
- Fork 이후 원본 원격 저장소에 변경 사항이 있을 경우
  1. 원본 원격 저장소(upstream) → `fetch` & `pull` → 나의 로컬 저장소
  2. 나의 로컬 저장소 작업 사항(커밋 이력) → `push` → 나의 원격 저장소(`origin`)
  3. 나의 원격 저장소(`origin`) → `PR` → 원본 원격 저장소(upstream)

<br>

***

### 참고

- [Git 공식문서](https://git-scm.com/docs)
- [팀 개발을 위한 Git, Github 시작하기](http://www.yes24.com/Product/Goods/85382769)
- [알아서 잘 딱 깔끔하고 센스있게 정리하는 Github 핵심 개념](https://m.yes24.com/Goods/Detail/108203273)
- [Git fetch: fork한 저장소를 원래 저장소의 최신 커밋 내역으로 바꾸기](https://chanhuiseok.github.io/posts/git-2/)
- [Github에서 협업을 위한 remote repository와 upstream 이해하기](https://pers0n4.io/github-remote-repository-and-upstream/)

