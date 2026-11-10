# ios-figma-png

一个面向 iOS 开发的 Codex 技能。它从 Figma 节点导出 3x PNG，用随技能分发的 TinyPNG CLI 压缩，再将结果放入 Xcode 图片集的 3x 槽位。例如，24 × 24 pt 的图标会以 72 × 72 px 导入。

## 安装

下载或克隆仓库，在仓库根目录运行以下命令。技能和 CLI 会一起安装。

```sh
skills_dir="${CODEX_HOME:-$HOME/.codex}/skills"
mkdir -p "$skills_dir"
cp -R skills/ios-figma-png "$skills_dir/"
```

在运行 Codex 的环境中设置 `TINIFY_API_KEY`，重启 Codex 后即可调用技能。CLI 需要 Python 3.8 或更高版本，只使用标准库。压缩时会将所选图片上传至 Tinify。[获取 TinyPNG API 密钥](https://tinypng.com/developers)

## 使用

给 Codex 提供 Figma 节点链接和目标资源名，例如：

> 用 $ios-figma-png 将这个 Figma 节点导入为 CloseIcon，放到 App 的 Assets.xcassets 中。

也可以单独调用已安装的 CLI：

```sh
python3 "${CODEX_HOME:-$HOME/.codex}/skills/ios-figma-png/scripts/tinypng" "CloseIcon@3x.png"
```

默认生成 `CloseIcon@3x.min.png`。用 `-o <路径>` 可以指定输出文件。
