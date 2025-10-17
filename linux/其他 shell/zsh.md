### 基础安装

```bash
sudo pacman -S zsh zsh-autosuggestions zsh-syntax-highlighting zsh-completions
```

```bash
chsh -s /usr/bin/zsh
```

```bash
vim ~/.zshrc
```

追加内容

```bash
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

### 安装主题

安装 zim 框架

```bash
curl -fsSL https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh
```

```bash
vim ~/.zimrc
```

追加内容

```bash
zmodule romkatv/powerlevel10k
```

```bash
zimfw install
```

> 可以使用 `p10k configure` 再次进入配置页

## 简单配置

追加 `~/.zshrc`

```bash
if [[ -o interactive ]]; then
    alias l='eza --icons -g -l'
    alias la='eza --icons -g -la'
    alias lt='eza --icons -g -T'
    alias cat='bat -p'
    alias rm='safe-rm'
    alias ..='cd ..'

    alias tock='tock -cms -C 6'
    alias inxi='inxi -Fazy'
    alias tmux='zellij'
    alias book='tldr'

    eval "$(zoxide init zsh)"
    eval "$(atuin init zsh --disable-up-arrow)"
fi

chpwd_print_path() {
  echo " 👉 $(pwd)"
}

autoload -U add-zsh-hook
add-zsh-hook chpwd chpwd_print_path

path=(
    $HOME/.local/bin
    $HOME/.cargo/bin
    $path
)
export PATH
```

更改 `~/.p10k.zsh`

```bash
  typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(
    # os_icon               # os identifier
    user
    host
    dir                     # current directory
    vcs                     # git status
    prompt_char             # prompt symbol
  )
```

...

```bash
  typeset -g POWERLEVEL9K_SHORTEN_STRATEGY=truncate_to_last
```

