# how to claude-code




> [!rf]
> 2026-01-03
> https://www.youtube.com/watch?v=1Tyn7r0AD14


---


- Hooks:
    - 지정된 트리거로 자동 실행되는 쉘 스크립트
- Plugins:
    - 외부에 두고 사용, 관리 용이, 확장성, version control
- Skills:
    - (유저 프롬프트를 보고) 자동으로 실행되는 커스텀 커맨드
    - 실행할지 질문
- SubAgents:
    - 하나의 일만 처리하는 전문 Agent
    - 병렬처리 가능
    - main context의 오염 방지(결과만 전달)
- Custom Slash Command:
    - 커스텀 커맨드
    - `/`로 직접 호출하여 사용


---



> this is my dotfiles. inspect and pick most 2 things to improve

● I'll explore your dotfiles to understand the structure and identify the most impactful improvements.

● Explore(Explore dotfiles structure)
  ⎿  Done (33 tool uses · 76.3k tokens · 2m 50s)

● Based on my exploration of your dotfiles, here are the 2 most important improvements:

  1. Security & Sensitive Data Management 🔒

  Issues found:
  - .commands file contains hardcoded IP address (3.39.132.9) and SSH key path (.ssh/dev-server.pem)
  - .aider.* files tracked in git (AI chat history/cache that may contain sensitive info)
  - Email address exposed in .gitconfig

  Impact: Security risk if this repo is pushed to public GitHub

  Fix:
  # Add to .gitignore
  echo ".aider.*" >> .gitignore
  echo ".commands" >> .gitignore

  # Create template system
  mkdir -p templates
  cp .commands templates/commands.template
  # Then sanitize .commands by replacing sensitive values with placeholders like {{SSH_KEY_PATH}}

  Files affected:
  - .commands (contains IP and SSH key paths)
  - .aider.chat.history.md, .aider.tags.cache.v3/ (AI artifacts)

  ---
  2. Path Standardization & Portability 🔧

  Issues found:
  - Hardcoded /home/sy/ paths throughout configs make them non-portable
  - Found in: bash.sub/alias.sh, tmux.conf, .commands, and others

  Impact: Can't easily use on multiple machines or share with others

  Examples:
  # In alias.sh (line ~70)
  bindsym $mod+i exec --no-startup-id bash ~/dotfiles/dot-config/bash.sub/vocal/vocal.sh

  # In tmux.conf
  bind-key -n C-M-p run-shell "bash /home/sy/dotfiles/dot-config/bash.sub/tmux/...

  Fix:
  Replace hardcoded paths with $HOME or create a DOTFILES variable:
  # Add to .bashrc or env.sh
  export DOTFILES="$HOME/dotfiles"

  # Then use throughout configs:
  bash "$DOTFILES/dot-config/bash.sub/vocal/vocal.sh"

  ---
  Would you like me to implement either or both of these improvements?
