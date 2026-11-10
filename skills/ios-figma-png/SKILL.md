---
name: ios-figma-png
description: 在 iOS 开发中从 Figma 设计节点提取或更新图片资源时使用。按原始设计尺寸导出 3x PNG，用附带的 TinyPNG CLI 压缩，再导入 Xcode Asset Catalog。
---

# 从 Figma 导入 iOS 图片

1. 找到本次要导入的图层、组件实例或切片，确认其导出边界和在 iOS 中的点尺寸。按目标模块的命名约定选择资源名与 `.xcassets`。
2. 从该节点直接以 **PNG、3x** 渲染到临时目录，命名为 `<资源名>@3x.png`。可用 Figma 的 Export 面板，或调用 `GET /v1/images/{file_key}?ids={node_id}&format=png&scale=3`，下载 `images[node_id]` 的渲染结果。截图、原始 image fill 和放大的 1x 导出都不能代替节点的 3x 渲染。[Figma 节点渲染 API](https://developers.figma.com/docs/rest-api/file-endpoints/#get-image)
3. 用本技能目录下的 `scripts/tinypng` 压缩每张导出图。CLI 读取 `TINIFY_API_KEY`，将图片上传至 Tinify。下面的 `skill_dir` 指包含本文件的目录，`work_dir` 指临时导出目录。

   ```sh
   python3 "$skill_dir/scripts/tinypng" "$work_dir/close@3x.png" -o "$work_dir/close.min.png" &&
     sips -g format -g pixelWidth -g pixelHeight "$work_dir/close@3x.png" "$work_dir/close.min.png"
   ```

4. 仅在 CLI 成功后导入。核对两份文件均为 PNG，像素宽高相同且对应设计的 3 倍导出尺寸。例如，24 × 24 pt 的切片应得到 72 × 72 px。缺少密钥、压缩失败或尺寸不符时，保留导出图并报告原因。
5. 将压缩结果以 `<资源名>@3x.png` 放入 `<资源名>.imageset`。在 `Contents.json` 的目标 `images` 条目填写该文件名及 `"scale": "3x"`。新建通用图片用 `"idiom": "universal"`，并添加 `"info": {"author": "xcode", "version": 1}`。更新已有资源时只改对应槽位，保留其他倍率、外观变体和渲染设置。新资源只提供 3x 文件，代码按资源名加载。[Apple Image Set 格式](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/ImageSetType.html)
