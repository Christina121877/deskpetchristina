# deskpetchristina · 桌面宠物「宠伴 / Lulu」技能包 [V6.7]

> 一个用 **PyQt6** 实现的透明背景桌面宠物：**9 种表情 + 各自专属动作特效 + 满屏自主游走 + 10 种点击交互 + 全屏/输入双信号勿扰 + 跨平台开机自启 + macOS 程序坞图标**。
> 一套源码，既能出 **macOS `.app`**，也能出 **Windows `.exe`**。
> 本仓库即 `windows-image-pet-builder` 技能（按需求改名为 `deskpetchristina`），`product/` 为唯一真源运行包。

---

## 🧰 技术栈

| 依赖 | 版本 | 用途 |
|---|---|---|
| `PyQt6` | >= 6.5.0 | GUI 框架，透明无边框窗体 + 系统托盘 |
| `Pillow` | >= 10.0.0 | 角色图加载、缩放、气泡合成 |
| `numpy` | >= 1.24.0 | 动画位移 / 弹性曲线数学计算 |
| `scipy` | >= 1.10.0 | 信号平滑（勿扰检测节拍） |
| `pyinstaller` | >= 6.0.0 | 跨平台打包（`.app` / `.exe`） |
| **Python** | 3.10+ | 运行环境 |

依赖见 `product/requirements.txt`，安装：`pip install -r product/requirements.txt`。

---

## ✨ 核心能力

- **9 态表情**：normal 点赞 / cheer 欢呼 / angry 生气 / surprised 惊讶 / shy 害羞 / sleep 睡觉 / love 爱心 / peace 胜利 / worried 担心，每种表情配独立动作曲线（呼吸、弹跳、震动、心跳等）。
- **满屏自主游走**：walk / swim / idle 三模式随机切换（每 2.8~6 秒重新决策），边缘反弹并水平镜像转身，约 35% 概率边走边冒泡。
- **10 种点击交互**：左键点击轮流触发动作 + 切表情 + 气泡；滚轮缩放；右键菜单（置顶 / 大小 / 退出 / 勿扰 / 开机自启）。
- **双信号勿扰（核心）**：
  1. **全屏隐身** — 前台程序全屏（演示 / 游戏 / 视频 / 全屏 IDE）时自动隐身。
     - macOS：`AXFullScreen` 属性（需授予「辅助功能 / 自动化」权限）
     - Windows：`GetForegroundWindow` + `GetWindowRect` 比对屏幕尺寸
  2. **输入隐身（V6.7 重点修复）** — 沿 AX 层级**向上回溯最多 8 层**判定是否处于文本输入，彻底解决微信等把输入框包在容器内导致打字时仍冒泡打断的问题。检测频率 `STEALTH_WATCH_MS=800`（0.8 秒）。
- **跨平台开机自启**：macOS 写 `~/Library/LaunchAgents/com.crystal.lulupet.plist`；Windows 写注册表 `HKCU\...\Run\LuluPetDesktop`。
- **macOS 程序坞图标**：应用名「宠伴」，去水印小团子图标（`product/assets/icons/宠伴.icns`）。

---

## 📂 目录结构（本仓库实际内容）

```
deskpetchristina/
├── SKILL.md                  # 技能说明（构建/动画/勿扰/开机自启全解）
├── README.md                 # 本文件
├── product/                  # ★ 唯一真源双平台运行包
│   ├── pet.py                # 主程序 V6.7（PyQt6 透明桌宠）
│   ├── character_*.png       # 9 张表情图（normal/cheer/angry/surprised/shy/sleep/love/peace/worried）
│   ├── requirements.txt      # Python 依赖
│   ├── build_mac_app.sh      # macOS .app 打包
│   ├── install_mac.sh        # 安装到 /Applications + 程序坞 + 开机自启
│   ├── build_win_exe.bat     # Windows .exe 打包（须在 Windows 执行）
│   └── assets/icons/         # 宠伴.icns（macOS）/ 宠伴.ico（Windows）应用图标
├── scripts/                  # 同步/工具脚本
│   ├── build_app_icon.py     # 图标最小入侵去水印（97.86% 原样保留）
│   ├── generate_sheet_v6.py  # 生成 9 态总览图 character_sheet.png
│   ├── process_v6.py         # 角色图处理流水线（去白底/水印/裁切/统一画布）
│   └── sync_lulu_app.sh      # 单向同步真源到 /Applications 与开发副本
└── references/               # 参考资源（占位）
```

> ⚠️ 封装纪律：`scripts/sync_lulu_app.sh` 由 `product/` 单向同步到 `/Applications/宠伴.app` 与开发副本，运行实例的 `pet.py` 不要手动改（会被同步覆盖）。

---

## 🚀 构建与运行

### 🍎 macOS（生成 `.app`）
```bash
cd product/
bash build_mac_app.sh      # 生成 dist/宠伴.app
bash install_mac.sh        # 安装到 /Applications + 程序坞 + 开机自启
open /Applications/宠伴.app
```
桌宠出现在屏幕**底部中央**，约 1 秒后开始自主游走。
> 后台 `python pet.py` 在 macOS 会被回收 GUI，必须用 `.app` 启动器（`LSUIElement` 脱离终端）。

### 🪟 Windows（生成 `.exe`）
> PyInstaller 不能跨平台生成 exe，**必须在 Windows 电脑上构建一次**。
```bat
:: 把整个 product/ 拷到 Windows，装好 Python 3.10+，双击：
product\build_win_exe.bat
:: 生成 dist\宠伴.exe
```
不打包也可直接 `python product/pet.py` 试跑（需先 `pip install -r product/requirements.txt`）。

---

## 🖱️ 操作

| 操作 | 效果 |
|---|---|
| 左键按住拖动 | 移动位置；松开后短暂停顿再继续游走 |
| 左键点击（不拖动） | 轮流触发 10 种动作，并切对应表情 + 气泡 |
| 滚轮 | 放大 / 缩小 |
| 右键 | 菜单（置顶 / 调整大小 / 退出 / 勿扰 / 开机自启） |
| 托盘图标 | 右键同样有菜单 |

---

## 📋 已知约束

- macOS 后台 `python pet.py` 启动后 GUI 会被回收，必须用 `.app` 启动器。
- Windows 打包需在 Windows 环境执行 `build_win_exe.bat`（PyInstaller 不能跨平台）。
- 输入隐身在 macOS 需先授予一次「辅助功能 / 自动化」权限（系统偏好设置 → 隐私与安全）。
- WorkBuddy 沙盒内 `launchctl load` 可能被拦截；安装脚本会写盘 plist 供真实会话手动 `launchctl load`，在真实终端执行则一键生效。

---

## 🧱 版本沿革

| 版本 | 关键能力 |
|---|---|
| V6.1 | 9 态表情 + 动作特效 + 自主游走 + 点击交互 |
| V6.2 | 全屏勿扰 |
| V6.3 | 输入勿扰（初版，仅查最聚焦元素） |
| V6.4 | 跨平台开机自启开关 |
| V6.5 | 应用图标 + 程序坞 |
| V6.6 | 启动路径 bug 修复（Dock 点开无响应）+ 图标最小入侵去水印 |
| **V6.7** | **微信输入隐身修复（AX 层级回溯）— 回复微信不再打断** |
