# 󰏢 alacritty

# NOTE: https://alacritty.org/config-alacritty.html

[general]
  import = [ "~/.config/alacritty/themes/themes/enfocado_dark.toml" ]

[window]
  # padding.x = 1
  # padding.y = 5
  # decorations = "Buttonless"

  # opacity = 0.95
  # blur = true

[font]
  normal.family = "D2Coding"
  size = 12.0
  offset.x = 1

[keyboard]
  bindings = [
    { key = "C", mods = "Control|Shift", action = "Copy" },
    { key = "v", mods = "Control", action = "Paste" },
    { key = "N", mods = "Control|Shift", action = "SpawnNewInstance" },
    { key = "i", mods = "Alt", action = "ToggleViMode" },
    { key = "Space", mods = "Control", action = "ReceiveChar" }
  ]

[colors]
  vi_mode_cursor = { background = "#FFA500" }
  search.matches = { background = "#FFFF00" }
  search.focused_match = { background = "#0000FF" }

[hints]
  alphabet = "jfkdls;ahgurieowpq"

[[hints.enabled]]
  command = "xdg-open" # Linux/BSD용 명령어
  # command = "open"   # macOS용 명령어
  # command = { program = "cmd", args = ["/c", "start", ""] } # Windows용 명령어

  hyperlinks = true
  post_processing = true
  persist = false

  mouse.enabled = true

  binding = { key = "u", mods = "Alt" }

  regex = "(https://|http://)[^\u0000-\u001F\u007F-\u009F<>\"\\s{-}\\^⟨⟩`]+"
