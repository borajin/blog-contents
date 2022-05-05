---
title: "Hugo 로 깃블로그 만들기"
subtitle: ""
description: "Hugo 로 깃 블로그 만들고 Git Pages 로 배포하기."
date: 2022-05-03
author: "진보라"
image: ""
tags: ["Blog"]
categories: ["Tech"]
---

처음에는 광고를 달아보자는 목적으로 블로그를 시작했다. 공개적으로 포스팅하기 위해 정확한 정보를 찾는 과정에서 많은 공부가 되었고 단순히 읽는 것보다 **기록하는 것이 더 뇌리에 남는다**는 것을 깨닫고 TIL 을 실천해보려고 한다.
> `TIL(Today I Learn)` 이란 오늘 배운 것을 기록하는 것이다.

여럿이서 하면 더 즐거울 것 같아 2주 전부터 블로그 스터디를 시작했다. 첫 주에는 블로그 플랫폼을 결정하고 첫 포스팅을 했다. 원래 이용하던 티스토리를 이용할까 했는데, 티스토리와 관련된 기능들이 컨텐츠 읽는 것을 방해한다는 느낌이 들었고 무엇보다 '나만의 블로그' 라는 느낌이 없어 직접 블로그를 만들기로 했다.

스터디원 중 한 분은 Git Issue 를 CMS로 사용해 블로그를 구축했다. 이후 나도 매번 글을 작성하고 커밋하는 작업을 피하기 위해 Git Issue 를 이용해볼까 한다. 일단은 Hugo 와 Git Pages 를 이용해 블로그를 만들었다.

---

## Hugo 를 선택한 이유

블로그는 정적 웹 사이트다. 유명한 정적 웹 호스팅 플랫폼으로는 `Jekyll`, `Hexo`, `Hugo` 가 있다. `Jekyll`은 레퍼런스가 가장 많지만 컨텐츠가 많아질 수록 속도가 느려진다는 이슈가 있어 남은 둘 중 고민 하다, `Hexo` 보다 레퍼런스가 적지만 `Go` 언어를 사용해서 **속도가 가장 빠르다**는 점에서 `Hugo` 를 선택했다.

---

## 블로그 만들기

제일 먼저 한 것은 테마 고르기다. 테마는 [Hugo 공식 페이지의 Themes 탭](https://themes.gohugo.io/)에서 볼 수 있다. 최대한 글을 보기 깔끔한 테마로 골랐다. 내가 고른 테마는 [hugo-theme-cleanwhite](https://github.com/zhaohuabing/hugo-theme-cleanwhite) 이다. 이 테마에는 Quick Start 가 제공돼서 그대로 따라하면 된다. 혹은 [Hugo Document - Quick Start](https://gohugo.io/getting-started/quick-start/) 를 참고해도 된다. 아래는 내가 블로그를 만들고 배포한 과정을 적었다.

### 1. hugo 프로젝트 생성 후 테마 적용하기

[Hugo Document - Hugo 설치 방법](https://gohugo.io/getting-started/installing/)을 참고하여 설치 후 Hugo Project 를 생성한다.

```bash
$ hugo new site git-blog
```

적용할 테마의 깃 주소를 복사해 themes 폴더 아래에 submodule 로 추가한다.

```bash
$ cd git-blog
$ git submodule add https://github.com/zhaohuabing/hugo-theme-cleanwhite.git themes/hugo-theme-cleanwhite
```

> 서브모듈로 추가하면 각각 독립적인 깃 저장소로 분리하여 관리할 수 있다는 장점이 있다.

`themes/hugo-theme-cleanwhite/exampleSite` 디렉토리의 `config.toml` 내용을 루트 폴더의 `config.toml` 파일에 덮어쓰고 내 블로그에 맞게 커스텀한다.

```toml
baseurl = "https://borajin.github.io"
title = "blog title"
theme = "hugo-theme-cleanwhite"

...

[[params.addtional_menus]] #상단 nav 메뉴 추가 파라미터
title =  "TIL"
href =  "/top/tils/"  #루트 디렉토리의 하위 디렉토리인 top 디렉토리의 tils.md 파일을 가리킴.

...

[params.social] #우측 프로필란 sns 버튼 추가 파라미터
github = "https://github.com/borajin"
...
```

### 2. 로컬로 실행해보고 글 작성하기

아래 명령어를 입력해 hugo project 를 실행한다. 1313번 포트로 접속하면 테마가 적용된 블로그가 뜬다.

```bash
$ hugo serve -t  hugo-theme-cleanwhite #테마 포함해 서버 실행

...

Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
```

hugo 로 post를 작성하려면 아래 명령어를 입력하면 된다. 테마마다 지정된 기본 템플릿 내용으로 루트 리렉토리 하위의 post 디렉토리가 생셩될 것이다.

```bash
hugo new post/file-name.md
```

상단에는 post 의 메타 정보를 입력한다. image 는 첨부할 이미지의 경로를 입력하면 된다. 보통 static 폴더 하위에 imgs 폴더를 생성해 그 경로를 입력한다.

```md
---
title: "Hugo 로 깃블로그 만들기"
subtitle: ""
description: "Hugo 로 깃 블로그 만들고 Git Pages 로 배포하기."
date: 2022-05-03
author: "진보라"
image: "imgs/post1.png"
tags: ["Blog"]
categories: ["Tech"]
---

...
```

### 3. git repository 연결 및 build 하기

hugo 는 repository 를 2개 가진다. 하나는 **hugo 프로젝트 전체를 저장할 용도**이고 다른 하나는 **build 한 파일을 저장할 용도**이다. 두 번째 repository 는 git pages 로 배포해야 하므로 이름을 `{username}.github.io` 으로 지어야 한다. 나는 이렇게 두 레파지토리를 생성했다.

- borajin-blog-contents
- borajin.github.io

> git pages 는 [username].github.io 라는 도메인만 제공한다. 직접 산 도메인을 연결하고 싶다면 github project setting 에서 설정해야 한다.

```bash
# 프로젝트 파일 레파지토리 remote
$ git remote add origin https://github.com/borajin/borajin-blog-contents.git

# 빌드파일 레파지토리 remote
$ hugo  # build 커맨드. public 디렉토리가 생성되고 그 하위에 빌드 파일이 저장된다.
$ git submodule add -b main https://github.com/borajin/borajin.github.io.git public
```

이후 hugo 프로젝트를 clone 하면 submodule 로 추가했던 테마나 public 폴더가 텅 비어있을 것이다. 서브모듈도 같이 클론하고 서브모듈의 업데이트 내용을 자동으로 반영하려면 아래 옵션을 덧붙여준다.  

```bash
git clone --recurse-submodules https://github.com/borajin/borajin-blog-contents.git
```

> 만약 main branch 를 찾을 수 없다는 에러가 뜨면 `git branch -M main` 를 실행한다.

### 4. git pages 로 배포하고 쉘 스크립트 만들기

빌드 파일을 push 하면 저절로 git pages 로 배포가 된다. `https://{username}.github.io/` 주소로 들어가면 확인할 수 있다.

```bash
# 빌드 파일 배포
$ cd public
$ git add .
$ git commit -m "첫 배포"
$ git push origin main

# 프로젝트 커밋
$ cd git-blog
$ git add .
$ git commit -m "첫 배포"
$ git push origin main

```

그런데 이 과정이 너무 귀찮아 하나의 명령어로 처리하고 싶다면 쉘 스크립트 파일을 생성하면 된다. `deploy.sh` 파일을 만들고 아래 내용을 복사한다.

```sh
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo -t hugo-theme-cleanwhite # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public
# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin main

# Come Back up to the Project Root
cd ..

# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source repos.
git push origin main

```

작성한 쉘 스크립트를 실행하면 명령어 한 줄로 배포가 된다.

```bash
$ chmod 777 deploy.sh #터미널 상에서 실행할 수 있도록 권한을 바꿔준다.
$ ./deploy.sh
```

만약 블로그 주소로 들어가도 변경사항이 적용 되지 않는다면 `깃 레파지토리` 에 들어가 우측 Languages 위의 `Environments` 를 확인해보자. `github-pages` 를 클릭하면 배포 상태가 어떤지 확인할 수 있다.

---

## 회고

이렇게 Hugo 와 Git pages 를 사용한 깃 블로그를 만들었다. 사실 테마만 몇번째 갈아치우고 있다. 그래도, 드디어 가독성 좋고 마음에 드는 테마를 찾아 정착했으니 꾸준히 포스팅하는 것만 실천하면 된다. _(제일 어려운 일!)_
