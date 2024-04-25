---
title: 'gatsby로 블로그 만들기 2탄: github pages 배포하기'
birth: '2023-05-30'
modified:
slug: '2023/05/make-gatsby-blog-2nd'
---

# 배포 과정

프로젝트를 배포 할 때는 역시 npm script를 작성해야 한다. 나의 최종 script는 아래와 같다.

`"deploy": "git checkout main && gatsby build --prefix-paths && gh-pages -f -d public -b deploy"`

총 3단락이다.

1. main으로 브랜치를 옮기고
2. build한다.
3. npm package: gh-pages를 활용해서 배포한다.

## 1. checkout branch

나는 로컬에서 배포용 브랜치를 따로 관리하지 않을거다. 오로지 최종 결과물인 main브랜치를 이용한다.  
애초에 main 브랜치에서 이 명령을 사용하더라도 결과값은 참이기 때문에 안전하다.

## 2. build

--prefix-path는 root/gatsby-config에서 설정 해 둔 pathPrefix를 갖다 쓰겠다는 플래그다.

github pages에 배포를 하려다보면 자동으로 부여 받는 주소에 base path로 해당 remote repository의 이름이 붙는다. 이것을 pathPrefix로 설정 해 주어야 한다.(gatsby입장에서는 모르니까)

## 3. gh-pages

#### -f 옵션

<details>
  <summary>gh-pages 패키지 <code>--no-history</code> 옵션 사용하기</summary>
  <div style="background-color: #E2EADD; padding: 10px">
  
  gh-pages 패키지의 옵션 중 아래와 같은 것을 찾을 수 있다.
  >-f, --no-history // Push force new commit without parent history
   
  gh-pages 패키지의 작동 방식을 보면 지정한 build directory를, 지정한 branch로 커밋하고, 이 branch를 github pages 서비스로 호스팅 한다. 그러니까 gh-pages를 실행 할 때마다 커밋 즉 커밋 히스토리가 쌓인다.
  
  처음에는 별거 아니어도 history가 쌓일수록 빌드 시간을 늘릴 수 있다고 한다. 이를 방지하는 옵션이다. `-f`라는 옵션 약어는 `git push --force`처럼 기존 히스토리에 강제로 덮어 씌워버리기 때문으로 보인다.
  </div>
</details><br>

#### -d 옵션

무엇을 배포 할 것인지인데, gatsby는 build하면 기본적으로 public이라는 폴더에 빌드 된다.

#### -b deploy

local이 아니라 remote 저장소에 어떤 브랜치로 배포 할 것인지를 뜻한다. 배포하려는 소스코드를 `-d` 옵션으로 선택했다면, 그것을 remote의 어느 브랜치로 push 할 것인지를 말이다.

remote repository의 setting > github pages 탭에서 (deploy로)지정은 한번 해 줘야 할 것이다.

# 블로그에 글 쓰기

`gatsby-source-filesystem` 패키지로 지정한 폴더에 자유롭게 글을 쓰고, `npm run deploy` 해 준다.

물론 적절하게 commit하고 main브랜치 히스토리 관리하는 것도 신경 쓰면서. 나는 글 쓰기 위한 커밋만 따로 관리하기 위해서 'write' 라는 commit prefix를 추가하여 사용하기로 했다.

# Reference

https://www.gatsbyjs.com/docs/how-to/previews-deploys-hosting/how-gatsby-works-with-github-pages/
