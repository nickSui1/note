## Linux 命令工具提示及美化

在 macOS 的 Terminal 中使用 **Oh My Zsh** 实现对 Linux 命令的提示和自动补全，可以通过以下步骤完成：

------

### **1. 确保已安装 zsh 和 Oh My Zsh**

- **验证 zsh 是否为默认 Shell**： 运行以下命令确认当前使用的 Shell 是否为 zsh：

  ```bash
  echo $SHELL
  ```

  如果输出中包含 `/bin/zsh` 或类似路径，则 zsh 已设置为默认 Shell。如果不是，运行以下命令设置：

  ```bash
  chsh -s /bin/zsh
  ```

- **安装 Oh My Zsh**： 如果尚未安装 Oh My Zsh，可以通过以下命令安装：

  ```bash
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
  ```

------

### **2. 启用命令提示和补全**

Oh My Zsh 提供了多种插件和配置，可以增强命令提示和补全功能。

#### **（1）启用默认补全插件**

编辑 `~/.zshrc` 文件，找到 `plugins=(...)` 行，并添加以下内容：

```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

这些插件的作用是：

- `zsh-autosuggestions`：自动提示历史命令或常用命令。
- `zsh-syntax-highlighting`：高亮当前命令的语法，错误的命令会显示为红色。
- `git`：提供 Git 命令的快捷提示。

**安装插件：** 在启用这些插件之前，需要手动安装它们：

- **安装 zsh-autosuggestions**：

  ```bash
  git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
  ```

- **安装 zsh-syntax-highlighting**：

  ```bash
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
  ```

#### **（2）启用补全框架**

Oh My Zsh 本身内置了补全支持，你可以通过以下命令确认补全功能已开启：

```bash
autoload -U compinit
compinit
```

如果补全功能未生效，尝试在 `~/.zshrc` 文件的末尾添加：

```bash
autoload -U compinit && compinit
```

------

### **3. 安装命令行增强工具**

为了让提示更加智能，可以安装以下工具：

#### **（1）安装 fzf（模糊搜索工具）**

`fzf` 可以增强命令提示和历史搜索：

```bash
brew install fzf
```

安装完成后，运行以下命令初始化：

```bash
$(brew --prefix)/opt/fzf/install
```

它将为终端历史记录和文件路径补全提供模糊搜索支持。

#### **（2）安装 tldr**

`tldr` 提供简化版的 Linux 命令手册，可以快速查看命令的用法：

```bash
brew install tldr
```

使用方法：

```bash
tldr ls
```

------

### **4. 更换主题以增强提示**

可以选择更直观的主题，如 **Powerlevel10k**。

- **安装 Powerlevel10k**：

  ```bash
  git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
  ```

  然后编辑 `~/.zshrc`，将 `ZSH_THEME` 设置为：

  ```bash
  ZSH_THEME="powerlevel10k/powerlevel10k"
  ```

- **配置 Powerlevel10k**： 重新启动终端，运行以下命令配置主题：

  ```bash
  p10k configure
  ```

------

### **5. 测试效果**

完成以上配置后，你应该能够看到以下功能：

1. 命令自动补全和历史提示（通过 `zsh-autosuggestions`）。
2. 命令语法高亮（通过 `zsh-syntax-highlighting`）。
3. 智能模糊搜索（通过 `fzf`）。
4. 美观且信息丰富的提示符（通过 `Powerlevel10k`）。

重启终端并运行常用命令，检查提示和补全是否正常工作！