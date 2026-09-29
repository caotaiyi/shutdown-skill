# shutdown skill
纯整活。

远程用完电脑，顺手让 agent 关掉。支持 Windows 和 Mac

## 用法

装好后，跟 agent 说：

```text
关机
```

也可以只输入 `$shutdown-now`，不用再补一句“立即关机”或“允许丢弃未保存内容”。自然语言调用需要 agent 支持自动选择 skill。

它会先保存当前项目，再关机。已经确认保存的文件不用重存；有项目内容没法保存或无法确认保存，就停下来报错。保存不包括 git commit、push 或部署。

远程用的话，skill 和 agent 都要在远程电脑上。在哪台电脑执行，就关哪台。连接断了是正常的。

## 安装

下载本仓库，或者：

```sh
git clone https://github.com/caotaiyi/shutdown-skill.git
cd shutdown-skill
```

把 `shutdown-now` 文件夹复制到 agent 的 skills 目录。Codex 默认位置是 `~/.codex/skills`；设置过 `CODEX_HOME` 的就用那个目录下的 `skills`。

**Windows / PowerShell**

```powershell
$skillRoot = if ($env:CODEX_HOME) { Join-Path $env:CODEX_HOME 'skills' } else { Join-Path $env:USERPROFILE '.codex\skills' }
New-Item -ItemType Directory -Path $skillRoot -Force | Out-Null
Copy-Item -LiteralPath '.\shutdown-now' -Destination $skillRoot -Recurse
```

**macOS / Terminal**

```sh
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R shutdown-now "${CODEX_HOME:-$HOME/.codex}/skills/"
```

让 agent 刷新技能列表，必要时重新打开会话，再说“关机”或调用 `$shutdown-now`。

## 命令

| 系统 | 实际命令 | 要求 |
| --- | --- | --- |
| Windows | `shutdown.exe /s /t 0` | 当前账户具备关机权限 |
| macOS | `sudo -n /sbin/shutdown -h now` | 当前执行环境具备可用的非交互 sudo 授权 |

Mac 需要已有的 sudo 授权。`-n` 表示需要密码就直接报错，不等输入，也不会替你改权限。相关说明见 [Apple 文档](https://support.apple.com/guide/terminal/enter-administrator-commands-apd5b0b6259/mac)。

## 注意

- 保存范围是当前项目里 agent 能访问的文件和编辑器内容。其他应用里的未保存内容要自己处理，尤其是 Mac。
- Windows 默认不强制关闭应用，所以应用可能挡住关机。
- 等当前项目正在写入的内容保存好就关机，不等其他下载、编译任务完成。
- 安装和讨论 skill 不会触发关机，要明确要求关机才执行。
- 做过格式和命令语法检查，没有执行关机测试。Mac 还没真机测过。

## License

滚木
