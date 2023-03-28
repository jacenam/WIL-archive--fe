<img src="https://ifh.cc/g/NLYOyZ.png" style="max-width: 100%" align="center">

### 목차

- [1 브랜치란](#1-브랜치란)
- [2 포인터란](#2-포인터란)
- [3 브랜치 상태, 생성 및 이동](#2-브랜치-상태,-생성-및-이동)
  - [3-1 브랜치 상태 확인하기](#3-1-브랜치-상태-확인하기)
  - [3-2 브랜치 생성하기](#3-2-브랜치-생성하기)
  - [3-3 브랜치 이동하기](#3-3-브랜치-이동하기)
  - [3-4 모든 브랜치 상태 확인하기](#3-4-모든-브랜치-상태-확인하기)
- [4 변경사항 복원하기](#4-변경사항-복원하기)
  - [4-1 restore 명령어로 변경사항 되돌리기](#4-1-restore-명령어로-변경사항-되돌리기)
  - [4-2 checkout 명령어로 변경사항 되돌리기](#4-2-checkout-명령어로-변경사항-되돌리기)

- [5 브랜치 삭제, 복구하기](#5-브랜치-삭제,-복구하기)
  - [5-1 브랜치 삭제하기](#5-1-브랜치-삭제하기)
  - [5-2 브랜치 복구하기](#5-2-브랜치-복구하기)

- [6 브랜치 병합하기](#6-브랜치-병합하기)
  - [6-1 Fast-forward Merge](#6-1-Fast-forward-Merge)
  - [6-2 Merge Commit(feat. No Fast-forward)](#6-2-Merge-Commit(feat.-No-Fast-Forward))
  - [6-3 Squash Merge](#6-3-Squash-Merge)
  - [6-4 Rebase Merge](#6-4-Rebase-Merge)
  - [6-5 Conflict](#6-5-Conflict)
- [7 최적의 브랜치 Merge 전략](#7-최적의-브랜치-Merge-전략)


***

<br>

## 1 브랜치란

브랜치는 Git을 활용해 협업할 때 가장 핵심적인 개념 중 하나다. 작업물(코드)의 버전 관리를 여러 사람이 함께한다면 한 명의 작업이 끝날때까지 기다리고 그 다음 사람이 작업하는 것은 비효율적일 것이다. Git에서는 오류나 충돌 없이 독립적인 공간에서 여러 기능들을 별도로 동시에 작업하고 이를 다시 하나의 결과물로 합칠 수 있도록 하는 기능들이 마련되어 있다. 이를 브랜치(Branch)와 머지(Merge)라고 부른다. 먼저 브랜치를 살펴보자: 

Git에서는 기본적으로 설정되어 있는 브랜치가 존재한다. 바로 `main` 브랜치다(`master`라고 부르기도 하는데 인권 관련의 어휘라는 이유로 `main`이라 부른다). 만약 혼자서 작업한다면 `main` 브랜치 하나로 버전 관리가 가능하다

<img src="https://ifh.cc/g/KLf2BP.png" style="max-width: 100%" align="center">

그러나 만약 작업자가 한 명이 아니라 여러 명이며 동시에 각각의 작업이 필요한 경우, `main` 브랜치에서 서브 브랜치를 추가하여 `main` 브랜치에서의 작업은 유지하되 추가 버전의 작업을 수행할 수 있게 된다. 이후 필요에 의해 각자의 작업물을 하나로 모을 수도 있고, 작업물을 합치지 않고 팀원 B의 브랜치를 삭제하여 `main` 브랜치의 버전 관리 그대로 유지할 수도 있게 된다

<img src="https://ifh.cc/g/xlAcqp.jpg" style="max-width: 100%" align="center">

<br>

## 2 포인터란

앞서 [`git log`](https://github.com/jacenam/WIL-archive/blob/main/Git/Git%20%EC%83%81%ED%83%9C%EC%99%80%20%EB%B3%80%EA%B2%BD%EC%82%AC%ED%95%AD%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.md#2-%EC%BB%A4%EB%B0%8B-%EB%82%B4%EC%97%AD-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0) 명령어 대해 언급한 적이 있다. 최근 커밋한 내역을 출력받아 최근 커밋 사항들을 조회할 수 있다. 이때 `(HEAD -> main)`이라 출력된 부분을 보면 이는 가장 최근에 커밋된 내역을 가리키며 이를 포인터(Pointer)라고 부른다. 즉, 포인터(`HEAD`)는 현재 작업 중인 브랜치의 최신 작업 버전을 가리킨다

```bash
git log

→ commit 8d608f67fac92c63d1cdcbb4bf827a6ba960c9ab (HEAD -> main)
  Author: jacenam <jace.nams@gmail.com>
  Date:   Wed Mar 15 19:22:46 2023 +0900

    second commit

  commit 3fcc857803e44458c545933d49e1deb4ca17b1ff
  Author: jacenam <jace.nams@gmail.com>
  Date:   Wed Mar 15 14:57:59 2023 +0900

    first commit
```

<img src="https://ifh.cc/g/QaSpJx.png" style="max-width: 100%" align="center">

앞서 언급한 `checkout` 명령어를 통해 이전 커밋했던 상태로 파일 버전을 되돌리면 `HEAD`는 되돌린 버전으로 변경된다

```bash
git checkout 3fcc857

→  HEAD is now at 3fcc857 first version
```

<img src="https://ifh.cc/g/GLxdhA.png" style="max-width: 100%" align="center">

포인터(`HEAD`)는 현재 브랜치와 커밋을 가리키기 때문에 Git에서  중요한 개념이다. 브랜치를 생성, 변경, 복원할 때  `HEAD`를 기준으로 모든 명령이 이뤄지기 때문이다

<br>

## 3 브랜치 상태, 생성 및 이동

### 3-1 브랜치 상태 확인하기

현재 파일(코드)의 브랜치 목록과 현재 브랜치를 확인할 수 있다. 만약 그동안 브랜치를 따로 생성하지 않고 커밋을 해왔다면, 현재 브랜치는 `main` 브랜치다

```bash
git branch

→ * main
```

### 3-2 브랜치 생성하기

브랜치를 현재의 `HEAD` 시점에서 새로 생성하려면 `git branch [브랜치 이름]`의 형태로 명령어를 입력하면 된다

```bash
git branch feature/1
```

### 3-3 브랜치 이동하기

여기서 주의해야 할 점은 브랜치를 생성한다고 해서 `HEAD`는 변경되지 않는다. 새로운 브랜치를 생성해서  `HEAD`를 변경하려면 `git switch [브랜치 이름]`의 형태로 명령어를 입력해야 한다

> 기존에는 브랜치를 변경하기 위해서 `git checkout [브랜치 이름]` 형태의 명령어를 사용했다. 그러나 `checkout` 명령어에 너무 많은 기능들이 몰려있어 Git 2.23.0 버전부터는 `checkout` 명령어를 `switch`, `restore`로 각각의 기능에 맞게 명령어를 분리했다

```bash
git switch feature/2
```

만약 브랜치를 새로 생성하면서 바로 `HEAD`를 변경하고자 하면 `git switch -c 브랜치 이름`의 형태로 명령어를 입력하면 된다

> `git switch -c [브랜치 이름]` 명령어에서 `-c`는 'create'의 약자다

```bash
git switch -c feature/3
```

### 3-4 모든 브랜치 상태 확인하기

위 3-1~3-3 작업을 완료하면 브랜치는 총 3개(`main`, `feature/1`, `feature/2`)가 생성된다. 이때 `git log` 명령어로 브랜치별 상황을 참고하려 하면 `HEAD` 기준의 브랜치 1개 그리고 `main` 브랜치 1개, 총 2개의 브랜치의 로그만 출력된다(`HEAD` 기준은 `feature/1` 브랜치다)

```bash
git switch feature/1
git log

→ commit 1a8e4ab23fc0675f0437a76d4284b43292d5fb1a (HEAD -> practice2)
  Author: jacenam <jace.nams@gmail.com>
  Date:   Fri Mar 17 15:38:35 2023 +0900

    second commit

  commit 328309682cc4c95af8341403e7b794436fe3f61d (main)
  Author: jacenam <jace.nams@gmail.com>
  Date:   Fri Mar 17 15:37:25 2023 +0900

    first commit
```

따라서 모든 브랜치(현재 총 3개)의 로그를 한번에 출력하기 위해서는 아래 명령어를 활용하면 된다. 모든 브랜치의 로그가 한 줄로 요약되어 그래프가 추가된 상태로 로그가 출력된다

```bash
git log --all --decorate --oneline --graph

→ * 6f0c227 (feature/2) third commit
  | * 1a8e4ab (HEAD -> feature/1) second commit
  |/  
  * 3283096 (main) first commit
```

<br>

## 4 변경사항 복원하기

사실 파일 복원하는 방법은 브랜치와 무관하다고 보일 수 있다. 그러나 파일 복원의 방식 중 브랜치와 연관이 있는 부분이 있어 브랜치 대주제에 파일 복원 내용을 추가했다. 파일(버전)을 복원하는 방법은 두 가지다: 

- `restore` 명령어를 통해 변경 사항을 되돌릴 수 있다

- `checkout` 명령어를 통해 변경 사항을 되돌릴 수 있다

### 4-1 restore 명령어로 변경사항 되돌리기

`restore` 명령어는 파일의 변경 사항을 복원하는 것과 `add` 명령어로 스테이지에 올라간 파일을 스테이지에서 내릴 때 사용된다

1. 아래의 명령어 순서대로 입력하여 파일을 생성한 후 커밋을 한다. `status` 명령어로 버전 관리 상태를 확인했을 때 커밋을 하지 않은 파일이 없다고 출력된다

   ```bash
   cd ~/Desktop
   mkdir git_folder
   cd git_folder
   
   git init
   echo "# good morning" >> README.md
   git add README.md
   git commit -m "restore practice"
   git status
   
   → On branch main
     nothing to commit, working tree clean
   ```

2. `vi [대상 파일 이름과 형식]` 형태의 명령어를 통해 파일을 수정한다

   > `vi` 명령어는 'visual editor'의 약자로 리눅스 혹은 MacOS의 터미널에서 대상 파일을 수정할 수 있게 에디터 모드로 진입하게 된다

   ```bash
   vi README.md
   ```

   그럼 편집 모드로 진입된다

   <img src="https://ifh.cc/g/ZvRwZh.jpg" style="max-width: 100%" align="center">

3. 이후 `i → [내용 수정] → esc → :wq`의 순서를 따라 `README.md` 파일의 내용을 수정한다

   > `i → [내용 수정] → esc → :wq`의 `i`는 'insert' 모드 상태로 `vi`에서 내용을 입력 및 수정할 수 있는 모드로 전환시켜준다. 또한, `:`는 입력 모드로 전환된 비주얼 에디터를 다시 명령어 입력 모드로 전환시켜주는 명령어다.  `wq`는 'write'과 'quit'의 약자로 현재까지 입력한 수정 내용을 최종적으로 'write'하고 비주얼 에디터 모드에서 빠져나올 수 있도록 'quit'한다는 의미다

   <img src="https://ifh.cc/g/GpK1Ak.jpg" style="max-width: 100%" align="center">

4. 여기서 `status` 명령어를 통해 버전 상태를 확인하면 변경사항이 있다는 내용이 출력된다. 즉, 처음 `echo` 명령어를 통해 생성한 파일 버전과 다르게 내용에 변경 사항이 있다는 의미다

   ```bash 
   git status 
   
   → On branch main
     Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
   	modified:   README.md
   
     no changes added to commit (use "git add" and/or "git commit -a")
   ```

5. `restore`  명령어를 통해 이전 상태로 변경사항을 되돌린다. `vi` 명령어로 다시 비주얼 에디터 모드로 진입하면 파일의 내용이 초기 버전으로 전환된 것을 확인할 수 있다

   ```bash
   git restore README.md
   git status
   
   → On branch main
     nothing to commit, working tree clean
   ```

   <img src="https://ifh.cc/g/R64X3v.jpg" style="max-width: 100%" align="center">

6. 만약 `add` 명령어를 통해 스테이지에 올라간 파일이 존재한다면, `git restore --staged [파일 이름]` 명령어를 통해 스테이지에 올라간 파일을 내릴 수 있다

   > `restore` 명령어 외에도 `reset` 명령어를 통해 스테이지에 올라간 파일을 내릴 수 있다. `git reset HEAD [파일 이름]`의 형태로 사용이 가능하다

   ```bash
   git add README.md
   git restore --staged README.md
   git status
   
   → On branch main
     Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
   	modified:   README.md
   
     no changes added to commit (use "git add" and/or "git commit -a")
   ```

### 4-2 checkout 명령어로 변경사항 되돌리기

`git checkout -- [파일 이름]` 형태의 명령어를 사용하면 대상 파일의 내용이 최신 커밋 바로 이전으로 복원된다. 단, `checkout`되어진 파일의 지워진 내용은 커밋을 하지 않았기 때문에 다시 복원할 수 없다

```bash
git checkout -- README.md
git status

→ On branch main
  nothing to commit, working tree clean
```

<br>

## 5 브랜치 삭제, 복구하기

### 5-1 브랜치 삭제하기

 `git branch -D [삭제할 브랜치 이름]` 형태의 명령어를 입력하면 브랜치 삭제가 가능하다

```bash
git branch -D feature/1

→ Deleted branch feature/1 (was c3900cd).

git branch 

→ * main
```

### 5-2 브랜치 복구하기

삭제된 브랜치는 복구하기 위해서는 먼저  `reflog` 명령어를 사용해야 한다. `reflog`의 출력 결과의 맨 앞줄 `c3900cd`는 [해시]() 값이다. 각각의 커밋 내역은 해당 해시 값과 연결되어 있으므로 `reflog` 명령어를 통해 삭제한 브랜치를 어느 시점으로 되돌릴 것인지 먼저 정하려면 해시 값을 확인해야 된다. 그 다음 `restore practice` 시점으로 브랜치를 복구하기 위해 해당 시점의 해시 값을 찾는다

> `reflog` 명령어는 'reference log'의 약자다. 브랜치 이동/생성, `HEAD` 전환, 커밋 내역 등 모든 로그를 참조할 수 있다는 의미다

```bash
git reflog

→ c3900cd (HEAD -> main) HEAD@{0}: checkout: moving from feature/1 to main
  c3900cd (HEAD -> main) HEAD@{1}: checkout: moving from main to feature/1
  c3900cd (HEAD -> main) HEAD@{2}: commit (initial): restore practice
```

이후 브랜치를 복구하는 `git switch -c [삭제한 브랜치명] [커밋 해시값]`의 형태로 명령어를 입력해준다

> `git switch -c [삭제한 브랜치 이름] [커밋 해시값] ` 명령어는 `git checkout -b [삭제한 브랜치 이름] [커밋 해시값]` 명령어와 동일하다. `git checkout -b`에서 `b`는 'branch'를 의미한다. `--branch`와 같은 의미다

```bash
git switch -c feature/1 c3900cd

→ Switched to a new branch 'feature/1'

git branch

→ main
  * feature/1
```

<br>

## 6 브랜치 병합하기

브랜치 병합은 기본적으로 브랜치 두 개를 하나로 병합하는 것이다. 브랜치별로 나누어서 작업했던 결과물을 하나의 브랜치로 병합(Merge)시켜주는 것이다. 단순히 하나의 브랜치로 합치는 것은 아니고, 각각의 브랜치에 대한 합집합을 구해서 합치는 것이다. 만약 merge가 정상적으로 이뤄지지 않을 시 충돌(Conflict)가 발생할 수 있으므로 브랜치 간 merge에 대해서 조심히 다뤄야 한다

### 6-1 Fast-forward Merge

Fast-forward Merge(빨리 감기 병합)은 가장 기본적인 merge 형태다. `main` 브랜치에서 `feature/1` 브랜치를 분기한 이후 `main` 브랜치에 추가적인 커밋이 없다면 `feature/1` 브랜치의 커밋 이력(변경 사항)을 `main` 브랜치에 그대로 merge 할 수 있다. 아래 예제를 살펴보자: 

아래 로그와 같이 `main` 브랜치에 세 번(`A`, `B`, `C`)의 커밋을 하고 새로운 `feature/1`이라는 브랜치를 생성해 여기에 추가적으 세 번(`D`, `E`, `F`)의 커밋이 이뤄졌다

```bash
git log --oneline

→ f21bdef (HEAD -> feature/1) F commit
  4f22469 E commit
  549b242 D commit
  e7c2ddf (main) C commit
  2255c06 B commit
  67c904b A commit
```

현재 로그 상태를 아래 그림과 같이 나타낼 수 있다: 

<img src="https://ifh.cc/g/7AroDB.jpg" style="max-width: 100%" align="center" >

`C` 커밋과 `F` 커밋의 변경사항을 살펴보면 아래와 같다:

```bash
README.md 파일의 변경사항:

C              F 
content 1      content 1
content 2      content 2
content 3      content 3
               content 4
               content 5
               content 6
```

(1)이처럼 `main` 브랜치의 변경사항이 `feature/1` 브랜치에서의 변경사항에 모두 동일하게 포함되며 (2) `feature/1` 브랜치가 `main` 브랜치로부터 분기된 이후 `main` 브랜치에 추가적인 커밋이 없다면, 두 브랜치를 merge 했을 때 최신 커밋의 형태를 그대로 가져가는 Fast-forward 형태의 merge가 이뤄진다. 이때, fast-forward merge는 모든 커밋의 히스토리(내역)를 그대로 유지하는 형태다. 즉, 두 브랜치를 merge 했어도 커밋은 여섯 번으로 유지된다([merge commit과는 다르다](#6-2-Merge-Commit))

<img src="https://ifh.cc/g/1MVtbl.jpg" style="max-width: 100%" align="center" >

브랜치를 merge 할 때는 Incoming(들어오는) 브랜치를 Receiving(받는) 브랜치에 merge 하는 것이다. 즉, merge를 받는 `main` 브랜치 기준에서 `merge` 명령어를 실행해야 한다

```bash
git switch main
git merge feature/1

git log --oneline

→ f21bdef (HEAD -> main, feature/1) F commit
  4f22469 E commit
  549b242 D commit
  e7c2ddf C commit
  2255c06 B commit
  67c904b A commit
```

### 6-2 Merge Commit(feat. No Fast-forward)

Merge commit은 `main` 브랜치에서 `feature/1` 브랜치를 분기한 이후 `main` 브랜치에 추가적인 커밋이 있다면, 두 브랜치의 커밋 이력(변경사항) 전체를 하나로 병합하여 새로운 커밋을 생성하는 형태의 merge다. 아래 예제를 살펴보자: 

아래 로그와 같이 `main` 브랜치에서 세 번(`A`, `B`, `C`)의 커밋, 새로운 `feature/1`이라는 브랜치를 생성해 여기에 추가적으로 두 번(`D`, `E`) 커밋, 그리고 마지막으로 다시 `main` 브랜치에서 한 번(`F`)의 커밋이 이뤄졌다

```bash
git log --oneline 

→ 2fb0d9f (HEAD -> main) F commit
  d064ed3 (feature/1) E commit
  361b0b5 D commit
  03bd59d C commit
  196b8ed B commit
  8979dec A commit
```

현재 로그 상태를 아래 그림과 같이 나타낼 수 있다: 

<img src="https://ifh.cc/g/TgY4Jm.jpg" style="max-width: 100%" align="center">

이처럼 `feature/1` 브랜치가 `main` 브랜치로부터 분기된 이후 `main` 브랜치에 추가적인 커밋이 있다면, 두 브랜치를 merge 했을 때 분기 시점으로 추가된 `feature/1`와 `main` 브랜치의 커밋들을 모두 하나로 합치는 형태의 merge가 이뤄진다. 이때, 아래 그림과 같이 Merge commit은 명칭의 의미답게 merge 시 하나의 새로운 커밋을 생성하게 된다: 

<img src="https://ifh.cc/g/Vsb4xl.jpg" style="max-width: 100%" align="center">

즉, 두 브랜치를 merge 했을 때 하나의 merge commit이 추가되어 예제에서는 총 일곱 번의 커밋 이력이 존재하게 된다. 그리고 Fast-forward merge와 동일하게, merge commit 또한 Incoming 브랜치를 Receiving 브랜치에 merge 하는 것이다

```bash
git switch main
git merge feature/1

git log --oneline

→ c6783d5 (HEAD -> main) G commit: main & feature/1 branch merged
  2fb0d9f F commit
  d064ed3 (feature/1) E commit
  361b0b5 D commit
  03bd59d C commit
  196b8ed B commit
  8979dec A commit
```

>  Fast-forward merge를 merge commit과 동일하게 merge 시 새로운 최신 커밋을 생성하고자 하면 아래 명령어를 입력하면 된다: 
>
> ```bash
> git switch main
> git merge --no-ff feature/1
> 
> git log --oneline
> 
> → c6783d5 (HEAD -> main, feature/1) G commit
>   f21bdef F commit
>   4f22469 E commit
>   549b242 D commit
>   e7c2ddf C commit
>   2255c06 B commit
>   67c904b A commit
> ```
>
> <img src="https://ifh.cc/g/LhN38V.png" style="max-width: 100%" align="center">

### 6-3 Squash Merge

Squash merge은 `main` 브랜치에서 `feature/1` 브랜치를 분기한 이후 `main` 브랜치에 추가적인 커밋이 있는 Merge Commit과 동일한 상태에서, Incoming 브랜치의 모든 커밋 내역을 squash하여 하나의 새로운 커밋을 Receiving 브랜치에 추가하는 방식으로 이뤄진다

아래 그림과 같이 `main` 브랜치에서 세 번(`A`, `B`, `C`)의 커밋, `feature/1` 브랜치에서 세 번(`D`, `E`, `F`)의 커밋, 그리고 다시 `main` 브랜치에서 세 번(`G`, `H`, `I`)의 커밋이 이뤄졌다고 가정하자

<img src="https://ifh.cc/g/s1yJdF.jpg" style="max-width: 100%" align="center">

이때, 아래의 명령어와 같이 Receiving 브랜치인 `main` 브랜치를 기준으로 `main`과 `feature/1` 브랜치를 squash merge한다면 아래 로그와 같은 결과를 얻을 수 있다. Merge의 커밋을 포함하여 총 열 번의 커밋 이력이 존재해야 하지만 일곱 번의 커밋 이력만 존재한다는 것을 볼 수 있다: 

```bash
git merge --squash feature/1

→ bf93c48 (HEAD -> main) J commit: main & feature/1 branch sqaush merged
  616cebb I commit
  320339e H commit
  839477d G commit
  c8dadfd C commit
  4e7a78a B commit
  f31ba80 A commit
```

위 로그를 아래와 같은 그림으로 나타낼 수 있다. 이렇듯, 두 브랜치를 squash merge 하게 되면 incoming 브랜치의 커밋 이력은 하나로 합쳐지며 커밋 자체(브랜치도 포함)가 사라진다. Squash merge는 버그 수정 등으로 커밋 내역이 많아져 지저분해진 커밋 내역을 깔끔하게 정리하기 위해서도 사용된다: 

<img src="https://ifh.cc/g/lhBfDD.jpg" style="max-width: 100%" align="center">

### 6-4 Rebase Merge

'Rebase'의 사전적 의미는 '새로운 기준을 설정하다'이다. 버전 관리 측면에서의 `rebase`는 base를 다시(re) 지정한다는 의미의 명령어다. 아래 예제를 살펴보자:

현재까지 특정 브랜치를 기준으로 새로운 브랜치(`feature/1`)가 분기되는 예시들을 살펴봤었다. 이때 `feature/1` 브랜치가 분기된 시점 이후 커밋 이력(`C`, `D`)의 base는 `main` 브랜치의 `B` 커밋이다

<img src="https://ifh.cc/g/LzdjAX.png" style="max-width: 100%" align="center">

여기서 `main` 브랜치를 Receiving 브랜치라 고려했을 때, `rebase` 명령어를 통해 기존 base를 기준으로 Incoming 브랜치의 커밋 이력을 merge 할 수 있다

> `rebase` 명령어에 의한 merge 과정에서 Conflict(충돌)가 발생한다면 각 커밋 별로 충돌을 resolve(해결)한 후, rebase 과정을 계속(`--continue`)해서 진행해야 한다

```bash
git rebase feature/1

→ Auto-merging README.md
  CONFLICT (content): Merge conflict in README.md
  error: could not apply c4a09e9... E commit(main)
  hint: Resolve all conflicts manually, mark them as resolved with
  hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
  hint: You can instead skip this commit: run "git rebase --skip".
  hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
  Could not apply c4a09e9... E commit(main)
 
git rebase --continue

→ [detached HEAD 4bd5b67] E commit(main) rebase merged
  1 file changed, 1 insertion(+)
  Successfully rebased and updated refs/heads/main.

git commit -am "E commit"

git log --oneline

→ 86f070b (HEAD -> main) F commit(main)
  4bd5b67 E commit(main) rebase merged
  550148c (feature/1) D commit(feature/1)
  b67f0fd C commit(feature/1)
  8918387 B commit(main)
  ea45f84 A commit (main)
```

위 rebase merge 진행 후 로그를 아래 그림과 같이 나타낼 수 있다. Receiving 브랜치의  base를 기준으로 rebase merge가 진행되며 merge가 종료된 후 base는 최신 커밋인 `F` 커밋으로 변경되었다. 만약 새로운 커밋 `G`가 이뤄지고 `G` 커밋 직후 새로운 브랜치가 생성되어 커밋들이 이뤄진다면 브랜치에서 이뤄진 커밋들의 base는 `G` 커밋이 되는 것이다

<img src="https://ifh.cc/g/joFZkQ.jpg" style="max-width: 100%" align="center">

그렇다면 만약 `rebase`의 기준이 `main` 브랜치가 아니라 `feature/1`가 된다면 rebase merge의 결과는 어떻게 달라질까? `feature/1` 브랜치가 `main` 브랜치로부터 분기된 시점 이후 커밋 이력(`C`, `D`)의 base는 `main` 브랜치의 `B` 커밋이다

<img src="https://ifh.cc/g/akvGXw.png" style="max-width: 100%" align="center">

만약  `feature/1` 브랜치를 Receiving 브랜치라 고려했을 때 `main` 브랜치를 Incoming 브랜치로 rebase할 시 base는 `main` 브랜치의 `F` 커밋으로 재설정되며, 새로 설정된 base의 뒤로 `feature/1` 브랜치의 커밋 이력이 merge 된다

```bash
git switch feature/1
git rebase main

→ Auto-merging README.md
  CONFLICT (content): Merge conflict in README.md
  error: could not apply 5595620... C commit(feature/1)
  hint: Resolve all conflicts manually, mark them as resolved with
  hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
  hint: You can instead skip this commit: run "git rebase --skip".
  hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
  Could not apply 5595620... C commit(feature/1)
  
  git rebase --continue
  
→ [detached HEAD 73f35c0] C commit(feature/1): feature/1 & main rebase merged
  1 file changed, 1 insertion(+)
  Auto-merging README.md
  CONFLICT (content): Merge conflict in README.md
  error: could not apply 931e50c... D commit(feature/1)
  hint: Resolve all conflicts manually, mark them as resolved with
  hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
  hint: You can instead skip this commit: run "git rebase --skip".
  hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
  Could not apply 931e50c... D commit(feature/1)

git rebase --continue
  
→ [detached HEAD e46e64f] H commit(feature/1): feature/1 & main rebase merged
	1 file changed, 1 insertion(+)
	Successfully rebased and updated refs/heads/feature/1
	
git log --oneline

→ e46e64f (HEAD -> feature/1) D commit(feature/1): feature/1 & main rebase merged
  73f35c0 C commit(feature/1): feature/1 & main rebase merged
  24ac9d7 (main) F commit(main)
  f61a420 E commit(main)
  e8320f9 B commit(main)
  fa48bb1 A commit(main)
```

위 로그를 아래 그림과 같이 나타낼 수 있다. `feature/1` 브랜치를 기준으로 `main` 브랜치를 rebase한 경우 `main` 브랜치의 커밋 내역이 먼저 오고(`A` → `B` → `E` → `F`), base는 `F` 커밋으로 새로 설정되어 새로운 base 뒤에 `feature/1` 브랜치의 커밋 내역인 `C`와 `D`가 오는 것이다

<img src="https://ifh.cc/g/mza47w.jpg" style="max-width: 100%" align="center">

### 6-5 Conflict

브랜치를 merge(Fast-forward, Merge Commit, Rebase, Squash 등)할 시 변경사항에 따라 conflict가 발생할 수도 있다. Conflict는 브랜치를 merge할 때 변경사항 중 같은 곳에 각각의 수정사항이 존재한다면 conflict가 일어난다. 예를 들어, 공통적으로  `main` 브랜치의 커밋 내역을 base로 두는  `feature/1` 브랜치와 `feature/2`의 변경 사항이 아래와 같다고 가정하자:  

```bash
README.md(main branch):        README.md(feature/1 branch):        README.md(feature/1 branch):

hello world                    hello world                         hello world
                               git merge study                     git merge study 
```

`feature/1`과 `feature/2` 브랜치를 merge하려 한다. 이 상태에서 merge를 시도할 시 같은 곳(두 번째 줄)의 내용이 다르기 때문에 conflict가 아래와 같이 발생한다. 이때 conflict가 발생한 시점을 찾아서 직접 수정하고 conflict 내용을 제거해야만 한다(공통 내용은 수정 범위에 들어가지 않는다)

```bash
git switch feature/1
git merge feature/2 

→ hello world
  <<<<<<< HEAD (Current Change)
  git merge study
  =======
  >>>>>>> feature/1 (Incoming Change)
  git conflict study
```

`<<<<<<< HEAD (Current Change)`와 `  =======` 구분선 사이의 충돌 대상은 현재 브랜치(Receiving 브랜치)의 내용(변경사항)이다:

```
<<<<<<< HEAD (Current Change)
git merge study
=======
```

`=======` 구분선과 `>>>>>>> feature/1 (Incoming Change)` 사이의 충돌 대상은 병합될 대상 브랜치(Incoming 브랜치)의 내용이다:

```bash
=======
>>>>>>> feature/1 (Incoming Change)
git conflict study
```

Conflict 내용을 변경할 때는 터미널에서 직접하는 것보다 VSCode를 활용하는게 좋다. VSCode 터미널에서 conflict 발생 시 아래와 같이 조금 더 쉽게 변경 가능한 기능이 제공된다

<img src="https://ifh.cc/g/daqn7X.png" style="max-width: 100%" align="center">

Conflict 내용을 수정한 후 merge를 다시 진행하고 커밋을 하면 아래와 같은 로그 결과를 확인할 수 있다:

```bash
git log --all --oneline --graph

→ *   68e04fe (HEAD -> feature/1) E commit(merged)
  |\  
  | * d820142 (feature/2) D commit(feature/2)
  * | 32aa9ee C commit(feature/1)
  |/  
  * d1df3bb (main) B commit(main)
  * 7c201b4 A commit(main)
```

<br>

## 7 최적의 브랜치 Merge 전략

사실 개발 프로젝트 그룹 혹은 회사의 팀별로 모두 선택하는 개발 및 배포 방식이 상이하기 때문에 최적의 브랜치 Merge 전략은 없다고 할 수 있다. 개발 포지션으로 실무에서의 경험이 없기 때문에 추후 실무에서 어떤 전략이 가장 나을지 다시 살펴보기 위해 그동안 검색해본 여러 정보를 정리해보려 한다. 아래 그림은 [우아한형제들 기술블로그](https://techblog.woowahan.com/2553/)에서 가져온 Git-flow를 가장 잘 대표하는 차트의 일부다: 

<img src="https://ifh.cc/g/kprwrK.jpg" style="max-width: 100%" align="center">

- **master(main):** 실제 사용자가 사용하게 될 서비스 출시를 위한 브랜치
- **develop:** 다음 출시 버전의 개발이 이뤄지는 브랜치
- **feature:** 다음 출시 버전의 세부 기능들을 기능별로 쪼갠 브랜치
- **release:** 정식 버전 출시를 위해 배포 준비를 하는 브랜치
- **hotfixes:** 출시 버전의 버그 수정을 위한 브랜치

일반적으로 `feature` 브랜치에서 각각의 세부 기능들을 개발하고 `develop` 브랜치에 merge하고, `develop` 브랜치의 개발 사항을 최종적으로 `main` 브랜치에 merge하여 서비스를 출시하는 순서를 띈다

`feature` 브랜치를 `develop` 브랜치에 merge할 시 Squash & merge 방식이 유용하다 한다. `feature` 브랜치에서 기능을 개발하며 발생하는 여러 잔업의 커밋 내역을 하나의 커밋으로 묶어 `develop` 브랜치에 병합하기 때문에 `develop` 브랜치에는 기능(feature) 단위로 커밋이 추가되어 한눈에 개발 상황을 볼 수 있다는 이점이 있다

`develop` 브랜치를 `main` 브랜치에 merge할 시 Rebase & merge 방식이 유용하다 한다. 만약 `develop` 브랜치를 `main` 브랜치에 squash merge하게 되면 그동안의 커밋 이력이 모두 사라져 기능에 문제나 버그가 발생될 시 롤백할 수 없게 되기 때문이다

<br>

---

### 참고

- [Git 공식문서](https://git-scm.com/docs)
- [W3docs - Git Merge](https://www.w3docs.com/learn-git/git-merge.html)
- [팀 개발을 위한 Git, Github 시작하기](http://www.yes24.com/Product/Goods/85382769)
- [알아서 잘 딱 깔끔하고 센스있게 정리하는 Github 핵심 개념](https://m.yes24.com/Goods/Detail/108203273)
- [러닝 브랜치](https://learngitbranching.js.org/?locale=ko)
- [라인 편집기 모드를 vi로 적용하기](https://knight76.tistory.com/entry/%EB%9D%BC%EC%9D%B8-%ED%8E%B8%EC%A7%91%EA%B8%B0-%EB%AA%A8%EB%93%9C%EB%A5%BC-vi%EB%A1%9C-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0-set-o-vi)
- [git merge 한 번에 정리하기: Fast Forward Merge, Commit Merge, Conflict Merge](https://kotlinworld.com/277)
- [Git Merge(feat. Github)](https://bcp0109.tistory.com/373)
- [Git Merge 종류](https://velog.io/@injoon2019/Git-Merge-%EC%A2%85%EB%A5%98)
- [Git Merge(fast-forward, 3-way, squash, rebase)](https://minoolian.github.io/tech/Merge.html)
- [Git의 다양한 브랜치 병합 방법(Merge, Squash, & Merge, Rebase & Merge)](https://hudi.blog/git-merge-squash-rebase/)
- [GitHub의 Merge, Squash and Merge, Rebase and Merge 정확히 이해하기](https://meetup.nhncloud.com/posts/122)
- [우린 Git-flow를 사용하고 있어요 - 우아한형제들 기술블로그](https://techblog.woowahan.com/2553/)

