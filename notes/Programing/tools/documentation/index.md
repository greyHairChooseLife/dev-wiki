# 󰏢 Document

---
title: 'mkdocs의 활용'
date: 2024-04-18
---

## 왜 mkdocs?

- 오픈소스

  내 문서를 내가 쓰는데 로그인 좀 그만하고싶다! 나를 위한 무슨 특별한 문서작성 스타일을 제공하지도 않으면서
  말이야.

  생성된 파일을 그들만의 저장소에 보관해야 하는것도 뭔가 내것이 아닌것만같은 느낌이고.

- markdown
- git과의 궁합


## 2. vim-wiki

### vim-wiki cheet-sheet

[web A](https://gist.github.com/drkarl/4c503bccb62558dc85e8b1bc0f29e9cb)
[web B](https://dokk.org/library/vimwiki_1.1.1_quick_reference_Posp%C3%ADchal_2011)


## temp

- 현재 디렉토리 내의 파일명에 공백이 있을 때, 공백을 '_'(under-bar)로 변경하기

  ```bash
  for file in *\ *; do mv "$file" "${file// /_}"; done
  ```

## 웹으로 배포하기

- [static site generator with markdown](https://dynalon.github.io/mdwiki/#!index.md)
- markdown-preview.vim이 사용하는 markdown parsing 관련 packages
  - `/home/sy/.local/share/nvim/site/pack/packer/start/markdown-preview.nvim/package.json`
- [web: good practice](https://mkaz.blog/)


## deprecated

> [!lg] Log 2024-08-24
> 처음엔 이렇게 gatsby로 시도했었다.
>
> [1탄: gatsby 이해하기](/Programing/tools/documentation/1탄:_gatsby_이해하기)
> [2탄: github pages 배포하기](/Programing/tools/documentation/2탄:_github_pages_배포하기)
