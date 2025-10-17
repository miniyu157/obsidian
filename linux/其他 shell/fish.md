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
# ~/.config/fish/functions/fish_prompt.fish

function fish_prompt
    # 保存最后一个命令的退出状态
    set -l last_status $status

    ## user@host
    # set_color green
    # printf '%s@%s' (whoami) (prompt_hostname)
    # set_color normal
    # printf ':'

    # 当前目录名称
    set_color --bold blue
    printf '%s' (basename $PWD)
    set_color normal

    # Git 分支信息
    set_color yellow
    printf '%s' (fish_git_prompt)
    set_color normal

    # 根据上一个命令是否成功，显示不同颜色的提示符
    if test $last_status -ne 0
        set_color --bold red
    else
        set_color --bold green
    end

    printf ' ❯ '
    set_color normal
end
```