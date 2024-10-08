# 󰏢 모든 keymap 확인하기


Neovim이나 Vim에서 사용자가 정의한 모든 커스텀 키 매핑을 보려면, 내장 명령어 `:map`을 사용할 수 있습니다. 이 명령은 다양한 모드(normal, insert, visual 등)에 대한 모든 매핑을 보여주며, 특정 모드에 대한 매핑만을 보려면, 모드에 맞는 명령어 변형을 사용할 수 있습니다.

| 기능                  | command |
|-----------------------|---------|
| all mode              | `:map`  |
| normal mode           | `:nmap` |
| insert mode           | `:imap` |
| visual mode           | `:vmap` |
| command-line mode     | `:cmap` |
| operator-pending mode | `:omap` |
| select mode           | `:smap` |
| terminal mode         | `:tmap` |

> ex)
> 사용자가 정의한 모든 일반 모드의 키 매핑을 보려면 `:nmap`을 입력합니다.

- 매핑의 정의 위치와 자세한 정보 보기:
  `:verbose` 키워드를 사용하면, 매핑이 정의된 파일과 줄 번호를 함께 볼 수 있습니다. 예를 들어, `:verbose nmap`은 일반 모드의 모든 매핑과 그 매핑이 정의된 위치를 보여줍니다.

- 특정 키 시퀀스에 대한 매핑 검색:
  매핑 명령 뒤에 키 시퀀스를 추가하여 해당 키 시퀀스에 대한 매핑 정보만을 필터링하여 볼 수 있습니다. 예를 들어, `<leader>a`에 대한 일반 모드 매핑을 검색하려면 `:nmap <leader>a`를 입력합니다.


> [!ye] 참고
>
> - 명령어 뒤에 입력하는 키 시퀀스는 정확해야 하며, 매핑 시 사용한 키 표기법과 일치해야 합니다. 예를 들어, `leader` 키는 보통 `<leader>`로 표기됩니다.
> - custom 설정이나 플러그인에 의해 추가된 매핑도 함께 나열됩니다.
> - `:Telescope keymap`도 가능
