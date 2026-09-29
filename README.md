# shutdown skill

给关机写了个 skill。

远程用完电脑，顺手让 agent 关掉。支持 Windows 和 Mac，指令尽量写短，能少花点 token 就少花点。

## 用法

装好后，跟 agent 说：

```text
$shutdown-now 立即关机，允许丢弃未保存内容。
```

它提醒一句，然后执行关机命令。

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

让 agent 刷新技能列表，必要时重新打开会话，再调用 `$shutdown-now`。

## 命令

| 系统 | 实际命令 | 要求 |
| --- | --- | --- |
| Windows | `shutdown.exe /s /f /t 0` | 当前账户具备关机权限 |
| macOS | `sudo -n /sbin/shutdown -h now` | 当前执行环境具备可用的非交互 sudo 授权 |

Mac 需要已有的 sudo 授权。`-n` 表示需要密码就直接报错，不等输入，也不会替你改权限。相关说明见 [Apple 文档](https://support.apple.com/guide/terminal/enter-administrator-commands-apd5b0b6259/mac)。

## 注意

- 先保存文件。Windows 会强制关闭应用，Mac 也不会挨个问你要不要保存。
- 这是立即关机，不等下载、编译或其他任务做完。
- 安装和讨论 skill 不会触发关机，要明确要求关机才执行。
- 指令短不代表零 token，具体消耗没测。自己敲命令当然更省。
- 做过格式和命令语法检查，没有执行关机测试。Mac 还没真机测过。

## License

MIT。
