---
hide:
title: 'nvim 플러그인에 이슈 남기기'
date: 2024-03-24
---

[여기](https://squidfunk.github.io/mkdocs-material/reference/)를 보면 아주 많은 formatting 기능이 소개 된다.
당장 사용 가능하게 out-of-box로 제공되기도 하고, `/mkdocs.yml`에서 한줄 한줄 추가해서 기능을 확장할 수도
있다.

나는 최대한 가볍게 사용하려고 했는데, 그래도 ==highlight== 랑 ~~취소선~~ 정도는 유용하겠다 싶어서 단축키로
지정해보려고 했다.

## 시도한 것

일단 주석을 통해 copilot에게 제안을 받아봤다. 너무 잘 만들어준다.

```lua
-- surround visual block with '=='
vim.api.nvim_buf_set_keymap(0, 'v', '<C-d>', '<Esc>`>a~~'<Esc>`<i~~'<Esc>', {noremap = true, silent = true})
```

근데 `lua`는 문자열 보간 기능 지원이 약한가보다. `\`을 활용해서 여러번 시도해봐도 잘 안된다.

## 결국엔

마침 사용중인 `markdowny.nvim`이라는 플러그인에서 거의 비슷한 기능을 제공한다. 이 플러그인을 파헤쳐서 몇가지
임의로 추가해 사용한다.

```lua
-- antonk52/markdowny.nvim

function M.mark()
  inline_surround('==', '==')
end

function M.tilde()
  inline_surround('~~', '~~')
end

vim.keymap.set('v', '<C-h>', ":lua require('markdowny').mark()<cr>")
vim.keymap.set('v', '<C-d>', ":lua require('markdowny').tilde()<cr>")
```

## 이슈 남기기

저장소에 [이슈](https://github.com/antonk52/markdowny.nvim/issues/11)를 남겼다.

플러그인 내부를 살펴보니 `inline_surround`라는 함수를 만들어서 사용중인데, 이것을 api로 제공해달라는 취지로.

이것만 해 줘도 너무 좋지만, 오픈소스에 PR을 남겨볼 기회도 될거같아서 한마디 남겨봤다. contributor가 되어보고
싶다.
