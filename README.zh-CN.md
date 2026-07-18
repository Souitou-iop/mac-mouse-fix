<div align="center">
  <img src="Markdown/Media/AppIconRound3.png" width="160" alt="Mac Mouse Fix 图标">
  <h1>Mac Mouse Fix 3.1.0</h1>
  <p>Souitou-iop 维护分支</p>
  <p><a href="Readme.md">English</a> | <strong>简体中文</strong></p>
</div>

本仓库是 [noah-nuebling/mac-mouse-fix](https://github.com/noah-nuebling/mac-mouse-fix) 的社区维护分支。在保留原项目功能、许可验证和整体交互的基础上，当前版本重点增强了 macOS 27 手势兼容性、按应用关闭平滑滚动、Helper 稳定性以及中文界面。

## 当前状态

| 项目 | 状态 |
|---|---|
| 版本 | **3.1.0（24832）** |
| 架构 | 本地打包版本仅提供 **Apple Silicon（arm64）** |
| 最低部署目标 | **macOS 12** |
| 稳定分支 | [`main`](https://github.com/Souitou-iop/mac-mouse-fix/tree/main) |
| 开发分支 | [`dev`](https://github.com/Souitou-iop/mac-mouse-fix/tree/dev) |

## 本分支的主要改进

### 按应用关闭平滑滚动

主窗口新增“应用”标签页。添加应用后，该应用位于前台时会立即使用系统原生的非平滑滚动；切换到其他应用后，自动恢复全局平滑滚动设置。

- 支持一次添加多个 `.app`。
- 重复添加时不会创建重复项目。
- 没有 Bundle ID 的应用使用标准化路径识别。
- 从列表移除后恢复继承全局平滑滚动设置。
- 列表过长时仅在页面内部滚动，不会继续拉高窗口。

应用排除规则只覆盖 `Scroll.smooth`。滚动速度、方向、修饰键、按键映射和其他设置仍继承全局配置。删除应用时也只删除平滑滚动覆盖，不会清除该应用可能存在的其他配置。

当前规则以前台应用为准；滚动未激活的后台窗口时，会继承前台应用对应的设置。这是一份专注于平滑滚动的排除列表，并不是完整的应用配置或鼠标配置系统。

### macOS 27 手势兼容

针对 macOS 27 中 Dock 不再接受单纯公开 `CGEvent` 手势字段的问题，本分支为合成手势补充了原始 IOKit HID 数据。涉及的用户功能包括：

- 切换桌面空间
- Mission Control
- App Exposé
- 显示桌面
- 其他基于 Dock Swipe 的拖拽手势

多显示器场景也加入了手势生命周期保护。当真实指针在拖拽过程中离开手势起始屏幕时，当前合成手势会在起始位置正常结束，避免 Dock 手势状态卡住。

### Helper 稳定性

前台应用变化、配置合并和派生 UI 状态现在统一在主线程刷新。滚动后台队列不再修改 `NSStatusItem` 或全局配置状态，从根源上避免了 Helper 因 AppKit 线程断言而退出、随后又被 `launchd` 自动拉起的问题。

### 原生标签动画与中文界面

“应用”页使用与原有标签相同的 AppKit 动画结构：窗口顶边保持不动，通过弹簧曲线改变高度，旧页面截图在背景中淡出。页面固定为 `420×260`，不会出现跨屏、黑色重影或多层内容错位。

新增界面文案提供英文、简体中文和繁体中文；其他语言回退为英文。

## 安装说明

本仓库目前主要维护源码分支，尚未发布独立的 Apple 公证安装包。原项目网站、Homebrew 和上游 Releases 中的安装包属于原项目，不包含本分支新增功能。

使用本地构建产物时：

1. 将完整的 `Mac Mouse Fix.app` 移到 `/Applications`。
2. 不要单独安装 `Mac Mouse Fix Helper.app`，Helper 已嵌入主 App。
3. 首次启动后，根据系统提示授予辅助功能等必要权限。
4. 在“应用”标签中添加需要关闭平滑滚动的应用。

本地测试包使用 ad-hoc 签名，仅适合个人测试，不等同于原开发者签名或 Apple 公证发行包。

## 自动更新注意事项

本分支保留了原项目的 Sparkle 正式更新源。未来接受上游自动更新时，安装内容可能被替换为原项目版本，从而失去本分支的应用排除和兼容性改进。接受更新前请先确认版本来源和更新说明。

## 从源码构建

推荐在 Apple Silicon Mac 上使用 Xcode：

1. 克隆仓库并切换到 `main`（稳定版）或 `dev`（开发版）。
2. 打开 `Mouse Fix.xcodeproj`。
3. 选择 `App - Release` scheme。
4. 选择 Apple Silicon Mac 作为目标。
5. 在 Signing & Capabilities 中配置自己的开发团队后构建。

命令行构建基线：

```bash
xcodebuild \
  -project "Mouse Fix.xcodeproj" \
  -scheme "App - Release" \
  -configuration Release \
  -destination "platform=macOS,arch=arm64" \
  ARCHS=arm64 ONLY_ACTIVE_ARCH=YES
```

本分支默认只验证 arm64 Release，不生成 x86_64 构建。正式对外分发仍需要有效的开发者签名和 Apple 公证。

## 分支约定

- `main`：当前稳定版本，现为 Mac Mouse Fix 3.1.0。
- `dev`：后续开发和验证分支；稳定后再同步到 `main`。

## 原项目与许可证

Mac Mouse Fix 由 Noah Nuebling 创建。本分支基于原项目源码开发，并保留原项目的许可、付费和试用系统。源码及衍生发行行为受仓库中的 [MMF License](License) 约束。

原项目资源：

- [官方网站](https://macmousefix.com/)
- [原项目仓库](https://github.com/noah-nuebling/mac-mouse-fix)
- [本分支英文 README](Readme.md)
