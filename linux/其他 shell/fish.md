## 基础安装

```bash
sudo pacman -S fish
```

```bash
chsh -s /usr/bin/fish
```

## 简单配置

`~/.config/fish/config.fish`

```bash
set -g fish_greeting ""

if status is-interactive
    alias l 'eza --icons -g -l'
    alias la 'eza --icons -g -la'
    alias lt 'eza --icons -g -T'
    alias cat 'bat -p'
    alias rm 'safe-rm'
    alias .. 'cd ..'

    alias tock 'tock -cms -C 6'
    alias inxi 'inxi -Fazy'
    alias tmux 'zellij'
    alias book 'tldr'

    zoxide init fish | source
    atuin init fish --disable-up-arrow | source

    function __fish_print_pwd_on_change --on-variable PWD
        if status --is-command-substitution
            return
        end
    
        set_color -o yellow
        echo " 👉 "(pwd)
        set_color normal
    end
end

```

`~/.config/fish/functions/fish_prompt.fish`

```bash
function fish_prompt
    set -l last_status $status

    printf '\n'

    set -l color_bg '44475a'
    set -l color_sep 'white'

    printf '╭─'

    set_color --background $color_bg

    set_color green
    printf '   %s ' (whoami)

    set_color $color_sep
    printf ''
    
    set_color green
    if test (pwd) = $HOME
        printf '   ~ '
    else
        printf '   %s ' (basename (pwd))
    end

    set -l git_prompt (fish_git_prompt)
    if test -n "$git_prompt"
        set_color $color_sep
        printf ''
        
        set_color yellow
        printf '  %s ' "$git_prompt"
    end
    
    set_color normal
    
    printf '\n'
    
    printf '╰─ '

    if test $last_status -ne 0
        set_color --bold red
    else
        set_color --bold green
    end
    
    printf '❯ '
    
    set_color normal
end

```