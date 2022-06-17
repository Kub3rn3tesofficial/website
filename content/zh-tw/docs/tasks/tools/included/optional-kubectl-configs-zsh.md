---
title: "zsh 自動補全"
description: "zsh 自動補全的一些可選配置"
headless: true
---
<!-- 
---
title: "zsh auto-completion"
description: "Some optional configuration for zsh auto-completion."
headless: true
---
-->

<!-- 
The kubectl completion script for Zsh can be generated with the command `kubectl completion zsh`. Sourcing the completion script in your shell enables kubectl autocompletion.

To do so in all your shell sessions, add the following to your `~/.zshrc` file:
-->
kubectl 透過命令 `kubectl completion zsh` 生成 Zsh 自動補全指令碼。
在 shell 中匯入（Sourcing）該自動補全指令碼，將啟動 kubectl 自動補全功能。

為了在所有的 shell 會話中實現此功能，請將下面內容加入到檔案 `~/.zshrc` 中。

```zsh
source <(kubectl completion zsh)
```

<!-- 
If you have an alias for kubectl, kubectl autocompletion will automatically work with it.
-->
如果你為 kubectl 定義了別名，kubectl 自動補全將自動使用它。

<!-- 
After reloading your shell, kubectl autocompletion should be working.

If you get an error like `complete:13: command not found: compdef`, then add the following to the beginning of your `~/.zshrc` file:
If you get an error like `2: command not found: compdef`, then add the following to the beginning of your `~/.zshrc` file:
-->
重新載入 shell 後，kubectl 自動補全功能將立即生效。

如果你收到 `2: command not found: compdef` 這樣的錯誤提示，那請將下面內容新增到 `~/.zshrc` 檔案的開頭：
```zsh
autoload -Uz compinit
compinit
```
