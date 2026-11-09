# pastime

mpv 便携配置（Windows 专用），面向 NVIDIA 显卡 + OLED HDR 显示器，使用
[ModernZ](https://github.com/Samillion/ModernZ) 界面 + [thumbfast](https://github.com/po5/thumbfast)
悬浮缩略图。

## 环境要求

- Windows
- mpv `0.42.0` 及以上
- 支持 D3D11 硬件解码的现代显卡（建议 NVIDIA）
- OLED HDR 显示器（SDR 显示器也能用，HDR 相关选项仅在需要时生效）

## 目录结构

```
mpv/
└── portable_config/
    ├── mpv.conf                 # 渲染 / 硬解 / 画质 / HDR / 音频等核心配置
    ├── input.conf               # 快捷键
    ├── menu.conf                # 右键菜单（mpv 0.42 内置）
    ├── script-opts/
    │   ├── modernz.conf         # ModernZ 界面样式
    │   ├── modernz-locale.json  # ModernZ 多语言
    │   └── thumbfast.conf       # thumbfast 缩略图选项
    ├── fonts/
    │   └── modernz-icons.ttf    # ModernZ 图标字体
    └── scripts/
        ├── modernz.lua          # ModernZ 界面脚本
        └── thumbfast.lua        # 悬浮缩略图脚本
```

## 安装

1. 下载 mpv Windows 便携版（如 `mpv-x86_64-<版本>-full.zip`）并解压。
2. 把本仓库的 `portable_config/` 整个复制到 mpv 根目录下，与 `mpv.exe` 同级。
3. 运行 `mpv.exe`。mpv 检测到同级存在 `portable_config/` 即以便携模式加载该配置。

## 主要配置

### 渲染与硬解

- 渲染 API 使用 `d3d11`，`swapchain-depth=2`——在 Windows + NVIDIA 上对 HDR 支持最稳、延迟更低。
- `hwdec=auto-safe`：安全硬解，避开问题解码器组合。
- `vd-lavc-film-grain=yes`：保留 AV1 影片颗粒，交由渲染器合成。
- `profile=high-quality`：高质量预设（缩放、抖动、抗振铃等）。

### 画质

- 缩放：`ewa_lanczossharp`（平衡锐度与振铃），抗振铃 `0.7`。
- 去色带：`deband` + 4 次迭代。
- 补帧：`interpolation=yes`，适合高刷新率 OLED。

### HDR

- `hdr-compute-peak=yes` + `tone-mapping=spline`：HDR 场景下自动计算峰值并进行色调映射（仅在显示器为 SDR 或峰值更低时生效）。

### 音频

- `ao=wasapi`：Windows 音频输出。

### 界面

- ModernZ 接管 OSC（`osc=no`），无边框窗口（`title-bar=no`），关闭内置进度条（`osd-bar=no`）。

### 其他

- 字幕语言偏好：中文（`chi,zho,zh,zh-CN,zh-TW,zh-HK`）。
- 截图：PNG + 高位深。
- 播放：退出记住进度、结束保持打开。

## 脚本

| 脚本      | 版本  | 说明                                     |
| --------- | ----- | ---------------------------------------- |
| ModernZ   | 0.3.3 | 现代 OSC：顶栏、时间轴、按钮、缩略图联动 |
| thumbfast | —     | 悬浮逐帧缩略图，由 ModernZ 时间轴调用    |

> ModernZ 内置了 thumbfast 集成；`menu.conf` 依赖 mpv `0.42` **内置**的
> `select`、`console`、`stats` 脚本，无需额外安装。

## 快捷键

| 按键            | 功能                           |
| --------------- | ------------------------------ |
| `SPACE`         | 播放 / 暂停                    |
| `←` / `→`       | 后退 / 前进 5 秒               |
| `↓` / `↑`       | 后退 / 前进 60 秒              |
| 滚轮上 / 下     | 音量 +5 / -5                   |
| `m`             | 静音                           |
| `[` / `]`       | 减速 / 加速 0.1×               |
| `BS`            | 恢复 1.0× 速度                 |
| `f`             | 切换全屏                       |
| `s`             | 截图（当前视频）               |
| `a` / `j` / `v` | 切换音轨 / 字幕轨 / 字幕可见性 |
| `i`             | 显示/隐藏统计信息              |
| `TAB`           | 显示/隐藏 ModernZ OSC          |
| `ENTER`         | 打开菜单                       |

## 右键菜单

mpv 0.42 内置 `menu.conf` 上下文菜单，默认**右键唤起**。本仓库的 `menu.conf`
提供播放控制、轨道切换、视频缩放/旋转/去色带/反交错、音视频/字幕延迟、窗口控制、
工具（复制路径、硬件解码切换、控制台、编辑配置）等条目。

## 注意事项

- 本配置为 **Windows 专用**：`ao=wasapi`、`gpu-api=d3d11`、
  `direct_io=yes` 等均为 Windows 专属选项。
- `scale=ewa_lanczossharp` 平衡锐度与振铃，若仍觉偏锐可退到 `ewa_lanczossoft`。
- `title-bar=no` 会关闭原生标题栏，窗口控制（关闭/最小化/最大化）由 ModernZ 顶栏提供。
