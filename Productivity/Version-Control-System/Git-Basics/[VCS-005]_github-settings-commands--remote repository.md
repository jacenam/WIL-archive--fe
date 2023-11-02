# Github 초기 설정 및 기초 명령어(feat. 원격 저장소)

**Table of Contents**

- [Github 원격 저장소 생성 및 연동하기](#Github-원격-저장소-생성-및-연동하기)
- [Github 원격 저장소에서 로컬 저장소로 내려받기](#Github-원격-저장소에서-로컬-저장소로-내려받기)
  - [git clone](#git-clone)
  - [git pull](#git-pull)

<br>

## Github 원격 저장소 생성 및 연동하기
앞서 Git으로 버전 관리할 [로컬 저장소](https://github.com/jacenam/WIL-archive/blob/main/Git/Git%20%EC%B4%88%EA%B8%B0%20%EC%84%A4%EC%A0%95%20%EB%B0%8F%20%EA%B8%B0%EC%B4%88%20%EB%AA%85%EB%A0%B9%EC%96%B4(feat.%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C).md#4-%EB%A1%9C%EC%BB%AC-%EC%A0%80%EC%9E%A5%EC%86%8C-%EC%83%9D%EC%84%B1-%EB%B0%8F-%EC%BB%A4%EB%B0%8B)에 대해 살펴봤다. 로컬 저장소에서도 작업물에 대한 버전 관리를 할 수 있지만 다른 개발자와 협업하려면 Git 호스팅 서비스인 Github에 원격 저장소(Remote Repository)를 생성하고 로컬 저장소와 동기화해야 한다. Github에서 원격 저장소와 하위 파일들을 직접 생성하는 법은 간단하니 터미널에서 파일을 생성하고 Github에 업로드(동기화)하는 방법을 살펴볼 예정이다

1. **[원격 저장소에서]** 먼저 Github에서 `README.md` 마크다운 파일 없이 새로운 원격 저장소를 생성한다(원격 저장소 이름: `practice`)

2. **[로컬 환경에서]** 터미널 경로 설정을 `Desktop`으로 변경한 뒤 앞서 언급했던 [`mkdir` 명령어](https://github.com/jacenam/WIL-archive/blob/main/Git/Git%20%EC%B4%88%EA%B8%B0%20%EC%84%A4%EC%A0%95%20%EB%B0%8F%20%EA%B8%B0%EC%B4%88%20%EB%AA%85%EB%A0%B9%EC%96%B4(feat.%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C).md#4-%EB%A1%9C%EC%BB%AC-%EC%A0%80%EC%9E%A5%EC%86%8C-%EC%83%9D%EC%84%B1-%EB%B0%8F-%EC%BB%A4%EB%B0%8B)를 통해 폴더를 생성한다. 그리고 터미널 경로를 다시 `practice` 폴더로 지정한다

    ```bash
    cd ~/Desktop
    mkdir practice
    
    cd practice
    ```

3. `practice` 폴더에 `touch` 명령어 혹은 `echo` 명령어를 통해 `README.md` 파일을 생성한다

    > [`touch` 명령어](https://github.com/jacenam/WIL-archive/blob/main/Git/Git%20%EC%B4%88%EA%B8%B0%20%EC%84%A4%EC%A0%95%20%EB%B0%8F%20%EA%B8%B0%EC%B4%88%20%EB%AA%85%EB%A0%B9%EC%96%B4(feat.%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C).md#4-2-%EB%A1%9C%EC%BB%AC-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EC%BB%A4%EB%B0%8B%ED%95%98%EA%B8%B0)는 지정한 경로에 파일을 생성해준다. `echo` 명령어는 `echo "# [파일에 작성할 내용]" >> [생성할 파일 이름과 형식]`의 형태로 지정한 경로에 파일을 생성해주고 `"# ~~~~"`에 지정한 내용을 생성할 파일에 작성해준다

    ```bash 
    touch README.md
    
    echo "# I've created a new .md file" >> README.md
    ```
    
    <img src="https://ifh.cc/g/MLqjWV.png" style="max-width: 100%" align="center">    

4. `practice` 폴더를 초기화해 Git 버전 관리가 가능한 상태로 만든다

    ```bash
    git init
    
    → Initialized empty Git repository in /Users/jace/Desktop/practice/.git/
    ```

5. `practice` 로컬 저장소 내 `README.md` 파일을 스테이지에 올리고 커밋을 한다

    ```bash
    git add README.md
    
    git commit -m "practice version"
    
    → [main (root-commit) b0d8f95] practice version
      1 file changed, 1 insertion(+)
      create mode 100644 README.md
    ```
    
6. 브랜치를 `main`으로 지정하고 `practice` 로컬 저장소와 Github의 `practice` 원경 저장소를 연동한다(브랜치에 대한 자세한 내용은 [여기]()를 참고하자)

    ```bash 
    git branch -M main
    
    git remote add origin https://github.com/jacenam/practice.git
    ```
    
    > `git branch -M main`은 메인 브랜치로 이동하라는 명령어다. 여기서 `M`은 'move'의 약자다

7. 로컬 저장소와 원격 저장소의 연동이 완료되면 로컬 저장소(로컬 브랜치)의 커밋 내용을 원격 저장소(원격 브랜치)로 동기화 시켜준다(`push` 명령어와 로컬/원격 브랜치에 대한 내용은 [여기]()를 참고하자)

    ```bash
    git push -u origin main
    
    → Enumerating objects: 3, done.
      Counting objects: 100% (3/3), done.
      Writing objects: 100% (3/3), 211 bytes | 211.00 KiB/s, done.
      Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
      To https://github.com/jacenam/practice2.git
      * [new branch]      main -> main
      branch 'main' set up to track 'origin/main'.
    ```
    
    > `git push -u origin main`에서 `u`는 'upstream'의 약자다. 즉, 로컬 브랜치에서 원격 브랜치로 변경사항(커밋 내용)을 '위로 보내는 것'이다. 반대로 원격 브랜치에서 로컬 브랜치로 버전 데이터를 내려받는 것은 'downstream'이라 볼 수 있다

<br>

## Github 원격 저장소에서 로컬 저장소로 내려받기
### git clone
원격 저장소의 코드와 파일 버전(최신/구) 전체를 동일하게 로컬 저장소(컴퓨터)로 내려받는 작업을 클론(clone)이라 부른다

<img src="https://ifh.cc/g/jOkDO5.jpg" style="max-width: 100%" align="center">

현재까지 작성했던 개인 Github 원격 저장소 `WIL-archive`를 로컬로 클론해보고자 한다

```bash 
cd ~/Desktop
mkdir WIL-archive
cd WIL-archive

git clone https://github.com/jacenam/WIL-archive.git .

→ Cloning into '.'...
  remote: Enumerating objects: 487, done.
  remote: Counting objects: 100% (316/316), done.
  remote: Compressing objects: 100% (249/249), done.
  remote: Total 487 (delta 153), reused 173 (delta 64), pack-reused 171
  Receiving objects: 100% (487/487), 145.69 KiB | 909.00 KiB/s, done.
  Resolving deltas: 100% (183/183), done.
```

클론 시 주의해야 할 점은 `git clone [클론할 원격 저장소의 HTTPS 주소]` 명령어 형태에서 맨 뒤에 ` .`을 추가해줘야 한다 

<img src="https://ifh.cc/g/Fcfl59.png" style="max-width: 100%" align="center">

` .`을 맨 뒤에 추가하지 않을 시 로컬 저장소로 사용할 폴더에 새로운 `WIL-archive` 폴더를 하나 더 생성하게 된다. 이러면 폴더 구조가 더 복잡해지기 때문에 항상 ` .`을 맨 뒤에 추가하자

<img src="https://ifh.cc/g/DpzsRn.png" style="max-width: 100%" align="center">

>  만약 특정 브랜치만 클론하고 싶다면 `git clone -b [브랜치 이름] [해당 저장소 URL]`의 형태로 명령어를 입력하면 된다

### git pull

만약 원경 저장소에서 업데이트된 변경사항은 있지만 로컬 저장소에는 해당 변경사항이 존재하지 않는다면 어떻게 동기화할 수 있을까? `pull` 명령어를 통해 로컬 저장소에 업데이트되지 않은 변경사항만 가져올 수 있다. 이는 팀 협업 시 타인이 원격 저장소로 업데이트한 변경사항을 나의 로컬 저장소로 동기화할 때도 사용된다


<img src="https://ifh.cc/g/lr8dN3.jpg" style="max-width: 100%" align="center">

```bash
git pull origin main

→ remote: Enumerating objects: 7, done.
  remote: Counting objects: 100% (7/7), done.
  remote: Compressing objects: 100% (4/4), done.
  remote: Total 4 (delta 3), reused 0 (delta 0), pack-reused 0
  Unpacking objects: 100% (4/4), 1.11 KiB | 227.00 KiB/s, done.
  From https://github.com/jacenam/WIL-archive
  * branch            main       -> FETCH_HEAD
   d5a4a4c..edfe420  main       -> origin/main
  Updating d5a4a4c..edfe420
  Fast-forward
  1 file changed, 10 insertions(+), 8 deletions(-)
```

> `git pull origin main`에서 `origin`은 코드(파일)가 보관된 원격 저장소를 의미하며 `main`은 변경사항을 `pull`할 원격 저장소의 브랜치를 의미한다. 예를 들어, 만약 `main`이 아닌 `jace`라는 브랜치의 코드를 로컬 저장소로 `pull`하고 싶다면 `git pull origin jace`의 명령어를 사용하면 된다

<br>

## 참고

- [Git 공식문서](https://git-scm.com/docs)
- [팀 개발을 위한 Git, Github 시작하기](http://www.yes24.com/Product/Goods/85382769)
- [알아서 잘 딱 깔끔하고 센스있게 정리하는 Github 핵심 개념](https://m.yes24.com/Goods/Detail/108203273)
