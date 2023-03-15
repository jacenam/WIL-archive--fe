<img src="https://ifh.cc/g/KcTXZ9.png" style="max-width: 100%" align="center">

### 목차

- [1 Git 설치하기](#1-Git-설치하기)
- [2 도움 명령어](#2-도움-명령어)
  - [2-1 버전 확인하기](#2-1-버전-확인하기)
  - [2-2 명령어 도움말 확인하기](#2-2-명령어-도움말-확인하기)
- [3 초기 설정 명령어](#3-초기-설정-명령어)
  - [3-1 사용자 정보 설정하기](#3-1-사용자-정보-설정하기)
  - [3-2 설정 정보 확인 및 변경하기](#3-2-설정-정보-확인-및-변경하기)
- [4 로컬 저장소 만들기](#4-로컬-저장소-만들기) 

***

<br>

## 1 Git 설치하기
Mac OS 기준으로 Git 설치하는 대표적인 방법인 두 가지가 존재한다:
1. 터미널에서 설치 확인하기(Mac에는 Git이 기본적으로 설치되어 있기 때문에 설치 여부 확인이 필요하다)
2. `homebrew`로 설치하기

### 1-1 Mac 터미널에서 Git 설치 확인하기
아래 명령어를 터미널에 입력한다
```bash
git 

usage: git [--version] [--help] [-C <path>] [-c <name>=<value>]
         [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
```
### 1-2 Homebrew를 통해 Git 설치하기
> `homebrew`란? Git, Java 등 개발에 필요한 환경을 셋팅하기 위해서는 공식 사이트에 접근하고 설치 파일을 다운받은 뒤 셋팅들을 설치해야하는 번거로움이 있다. `homebrew`는 애플 혹은 리눅스 운영체제에서 지원하지 않는 관리 도구 기능들을 터미널에서 명령어 몇 줄을 실행하는 것만으로도 개발에 필요한 환경을 패키지 형태로 편리하게 설치 및 제거할 수 있도록 도와주는 Mac용 패키지 관리 도구다. 

아래 명령어를 터미널에 입력한다(아래 명령어는 [homebrew 공식 홈페이지](https://brew.sh/) 첫 화면에서 확인할 수 있다)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
이후 터미널에서 `Password` 문구가 출력되면 사용자의 Mac 로그인 비밀번호를 입력한다. 그리고 아래 명령어를 입력하여 `homebrew`가 잘 설치되었는지 버전 확인을 한다
```bash
brew --version

→ Homebrew 3.6.14
Homebrew/homebrew-core (git revision c3f1a0d7d9a; last commit 2022-12-06)
```
`zsh: command not found: brew` 오류가 출력되면 아래 명령어를 입력하여 오류를 해결해준다 
```bash
eval $(/opt/homebrew/bin/brew shellenv)
```
homebrew의 버전 확인이 정상적으로 출력되면 아래 명령어를 통해 Git을 설치한다
```bash
brew install git
```
Git의 설치가 완료되면 Git이 잘 설치되었는지 버전 확인을 한다
```bash
git --version

→ git version 2.38.1
```

<br>

## 2 도움 명령어

### 2-1 버전 확인하기
설치한 Git의 버전을 확인한다
```bash
git --version 
git -v

→ git version 2.38.1
```

### 2-2 명령어 도움말 확인하기
아래 명령어들을 통해 Git에 대한 명령어 도움말을 터미널에 출력해서 확인할 수 있다 
```bash
git help -a: 명령어 상세 출력
git 대상 명령어 -h: 대상 명령어의 옵션 보기
git help 대상 명령어: web에서 대상 명령어 상세 보기
```

<br>

## 3 초기 설정 명령어
Git에 기본 설정을 변경하는 명령어의 구조는 아래와 같다: 
```bash
git config 대상 명령어: 한정적으로 대상 명령어대로 설정한다
git config --global 대상 명렁어: 전역으로 대상 명령어대로 설정한다
```
### 3-1 사용자 정보 설정하기
Git에 사용자 정보를 설정하는 것은 매우 중요하다. 사용자의 이름과 이메일을 제대로 입력하지 않으면 추후 Git을 사용하는 도중에 기타 오류 혹은 커밋 메세지 오류가 발생할 수도 있다. 아래 명령어를 통해 사용자 정보를 설정한다
```bash 
git config --global user.name "jacenam"
git config --global user.email jace.nams@gmail.com
```
### 3-2 설정 정보 확인 및 변경하기
아래 명령어를 통해 사용자 정보가 정상적으로 설정이 되어 있는지 확인할 수 있다. `list` 명령어는 사용자 정보 뿐만 아니라 사용자가 지정한 설정의 내역을 확인해볼 수 있으며 이를 통해 기존 설정을 변경 혹은 제거할 수 있다
```bash
git congfig --list

→ user.email=jace.nams@gmail.com
  user.name=jacenam
```
아래 명령어를 통해 설정을 삭제할 수도 있다
```bash
git config --unset user.name
git config --unset user.email
```
만약 위 명령어들로 사용자 정보 설정이 삭제되지 않는다면 이는 `global` 옵션이 추가되어서 그런 것이다. `global`로 설정된 사용자 정보는 아래 명령어를 통해 삭제할 수 있다
```bash
git config --unset --global user.name
git config --unset --global user.email
```

<br>

## 4 로컬 저장소 만들기
사용자의 로컬 환경(컴퓨터)에서 Git으로 버전 관리할 저장소를 생성해야 한다. 로컬 저장소를 생성하는 방법은 두 가지다: 
1. 데스크탑(Mac OS)/바탕화면(Window OS) 또는 이미 생성된 폴더에 직접 새로운 폴더(디렉토리)를 생성하는 방법
2. 터미널에서 명령어를 통해 새로운 폴더(디렉토리)를 생성하는 방법

> 데스크탑 또는 이미 생성된 폴더에 디렉토리를 직접 생성하는 방식은 이미 알고 있으니, 터미널에서 명령어를 통해 새로운 디렉토리를 생성하는 방법에 대해 정리하고자 한다. 터미널에서 `mkdir 디렉토리 이름` 명령어를 활용하면 원하는 위치에 로컬 저장소로 이용할 새로운 디렉토리를 생성할 수 있다

먼저 아래 명령어를 통해 디렉토리를 생성할 폴더(디렉토리) 경로를 설정한다(데스크탑에 디렉토리를 생성할 것이다). `cd`는 'change directory'의 약자다

```bash
cd ~/Desktop
```

이후 `mkdir` 명령어를 통해 로컬 저장소로 이용할 새로운 디렉토리를 생성하고 `cd` 명령어를 통해 새로 생성한 디렉토리로 경로를 변경한다. `mkdir`는 'make directory'의 약자다

```bash
mkdir local

cd local
```

> 만약 디렉토리 안에 디렉토리를 생성하고자 하면 혹은 `mkdir -p 디렉토리 이름 1/디렉토리 이름 2` 명령어를 통해 계층형 디렉토리를 동시에 생성할 수 있다

`local` 폴더(디렉토리)를 초기화하여 Git으로 버전 관리할 로컬 저장소로 변경한다. `init`은 'initialize'의 약자다. 선택한 디렉토리를 초기화해서 `.git` 저장소로 재설정하는 것이다

```bash
git init

→ Reinitialized existing Git repository in /Users/jace/Desktop/local/.git/
```

여기서 주의해야 할점은 지정한 디렉토리를 `.git` 로컬 저장소로 초기화해준다 하더라도 실제 `local` 폴더(디렉토리) 내부에는 `.git` 저장소가 보이지 않을 것이다. 이는 특정 시스템 및 백업 파일을 표시하지 않기 때문인데, `shift + command + .` 단축키를 누르면 숨겨진 파일 혹은 폴더를 표시할 수 있다

<img src="https://ifh.cc/g/CtmkvJ.gif" style="max-width: 100%" align="center">

<br>

***

### 참고
- [Git 공식문서](https://git-scm.com/docs)
- [팀 개발을 위한 Git, Github 시작하기](http://www.yes24.com/Product/Goods/85382769)
- [알아서 잘 딱 깔끔하고 센스있게 정리하는 Github 핵심 개념](https://m.yes24.com/Goods/Detail/108203273)
- [리눅스 명령어(디렉토리 생성) 사용법 & 옵션 정리](https://coding-factory.tistory.com/753)
