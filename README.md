# ios-figma-png

一个面向 iOS 开发的 Codex 插件。它从 Figma 节点导出 3x PNG，用内置的 TinyPNG CLI 压缩，再将结果放入 Xcode 图片集的 3x 槽位。例如，24 × 24 pt 的图标会以 72 × 72 px 导入。

## 安装

在装有 Codex CLI 的终端中运行以下命令，添加 GitHub 插件市场并安装插件。

```sh
codex plugin marketplace add jccapril/ios-figma-png
codex plugin add ios-figma-png@jccapril
```

安装后打开新的 Codex 聊天。图片压缩需要 Python 3.8 或更高版本，压缩时会将所选图片上传至 Tinify。

## 首次获取 API Key

在 Codex 中输入「用 iOS Figma PNG 插件帮我配置 TinyPNG API Key」。首次申请时，插件会询问**用户名和邮箱**，有浏览器工具时会代填官方表单。按页面提示提交并完成验证，用激活邮件中的链接进入控制台，复制个人 API Key。已有密钥时，按插件给出的终端命令在本机保存即可。[TinyPNG 官方申请说明](https://tinypng.com/developers)

也可从仓库根目录在本机终端运行向导：

```sh
./tinypng setup
```

向导自动打开官方表单供你填写用户名和邮箱。如果当前 Python 环境装有 Playwright，且本机装有 Chrome，向导会在终端询问这两项并预填表单。注册后将 API Key 粘贴回终端，输入时不可见。已有密钥可运行 `./tinypng setup --existing` 直接保存。

个人密钥以仅当前用户可读写的权限保存到 `~/.config/ios-figma-png/api-key`，重装技能后仍可使用。`TINIFY_API_KEY` 环境变量优先。如果两者均未配置，CLI 仍支持旧版技能内的 `scripts/.tinify-key`。

## 使用

给 Codex 提供 Figma 节点链接和目标资源名，例如：

> 用 iOS Figma PNG 插件将这个 Figma 节点导入为 CloseIcon，放到 App 的 Assets.xcassets 中。

如果已克隆仓库，也可以在仓库根目录单独运行 CLI。

```sh
./tinypng "CloseIcon@3x.png"
```

默认生成 `CloseIcon@3x.min.png`。用 `-o <路径>` 可以指定输出文件。
