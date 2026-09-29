# shutdown skill

让 AI 帮你关机。对，就这点事。

你说「关机」，它花半天研究操作系统，列三个方案，再问你要不要继续。

电脑没关，token 先下班了。

所以有了这个项目：一个很短的 skill，授权清楚之后，调用一次系统命令，收工。

## 怎么喊它

安装后，在支持本地 skills 和终端工具的 agent 里输入：

```text
$shutdown-now 立即关机，允许丢弃未保存内容。
```

它会先提醒一句，然后立即发起关机。实际关机耗时由操作系统决定。

远程使用时，把 skill 安装在远程电脑上，并在那台电脑运行的 agent 里调用。命令在哪台机器执行，哪台机器就关机。它不会自己找 IP、连接 SSH，或者替你挑一台电脑。

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

## 里面有什么

```text
shutdown-now/
├── SKILL.md
└── agents/openai.yaml
```

没有服务，没有常驻进程，没有依赖安装。README 留给人看，运行时只需读取短小的 `SKILL.md`。

| 系统 | 实际命令 | 要求 |
| --- | --- | --- |
| Windows | `shutdown.exe /s /f /t 0` | 当前账户具备关机权限 |
| macOS | `sudo -n /sbin/shutdown -h now` | 当前执行环境具备可用的非交互 sudo 授权 |

Mac 的 `-n` 会在需要密码时直接失败，不挂在那里等输入。权限不足就报告错误，skill 不收密码、不改 sudoers。Mac 管理员命令的权限说明见 [Apple Terminal 文档](https://support.apple.com/guide/terminal/enter-administrator-commands-apd5b0b6259/mac)，关机命令见 [Apple 命令行手册](https://www.apple.com/server/docs/Command_Line_v10.4_2nd_Ed.pdf)。

## 省到什么程度

指令尽量短，流程只留一次执行。不重复确认，不查文档，不轮询关机进度，不失败重试。

通过 AI 关机仍然要消耗 token。具体花多少，取决于模型、会话上下文和工具开销；这里没有编一个「节省 99%」的数字。

真要零 token，自己运行系统命令就行。这个项目负责满足「我就想让 AI 来关」的那点小坚持。

## 动手前看一眼

**未保存内容可能丢失。Windows 会强制关闭应用；Mac 命令也不会逐个询问你是否保存。**

立即关机不会等下载、编译或其他任务结束。提到、安装、修改这个 skill 都不算授权关机，用户要求实际关机才执行。它也不会绕过 agent 自身的工具权限。

完成时远程连接可能直接断开，看不到最后一句回复很正常。

本项目做了 skill 格式校验和命令语法检查，发布时没有实际执行关机；macOS 关机尚未在真机验证。

## 贡献

欢迎让它更短、更准。请别让关机多出五个依赖。

## License

MIT。拿去用，记得保存文件。
