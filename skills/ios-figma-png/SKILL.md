---
name: ios-figma-png
description: 在 iOS 开发中从 Figma 导出 3x PNG 并压缩导入 Xcode；也用于首次申请或配置此技能的 TinyPNG API Key。
---

# 从 Figma 导入 iOS 图片

## 首次申请或配置密钥

用户要求申请密钥或 CLI 提示未配置时，提供以下引导。已有密钥的用户直接到第 3 步。

1. 有浏览器工具时，在一条消息中询问「你要用什么用户名（Name）和邮箱申请 TinyPNG API Key？我会将这两项填入官方申请表。」如果用户已指定本次申请的用户名和邮箱，直接使用。没有浏览器工具时，给出本技能 CLI 的 `setup` 终端命令，由向导打开申请页并收集两项资料。
2. 代填时打开 [TinyPNG 开发者申请页](https://tinypng.com/developers)，在 **Get your API key** 表单填入 Name 和 Email Address。请用户核对并提交 Create API key，按页面提示完成人机验证，再从激活邮件进入控制台复制密钥。
3. 让用户在自己的终端运行 `python3 "<技能目录>/scripts/tinypng" setup --existing`，给出本次安装的实际路径。已在 `setup` 向导内时，直接粘贴到其隐藏输入提示。密钥只在本机输入，避免发到聊天里。保存成功后继续之前的压缩任务。

## 导入图片

1. 找到本次要导入的图层、组件实例或切片，确认其导出边界和在 iOS 中的点尺寸。按目标模块的命名约定选择资源名与 `.xcassets`。
2. 从该节点直接以 **PNG、3x** 渲染到临时目录，命名为 `<资源名>@3x.png`。可用 Figma 的 Export 面板，或调用 `GET /v1/images/{file_key}?ids={node_id}&format=png&scale=3`，下载 `images[node_id]` 的渲染结果。截图、原始 image fill 和放大的 1x 导出都不能代替节点的 3x 渲染。[Figma 节点渲染 API](https://developers.figma.com/docs/rest-api/file-endpoints/#get-image)
3. 用本技能目录下的 `scripts/tinypng` 压缩每张导出图。CLI 自动读取已配置的密钥。下面的 `skill_dir` 指包含本文件的目录，`work_dir` 指临时导出目录。

   ```sh
   python3 "$skill_dir/scripts/tinypng" "$work_dir/close@3x.png" -o "$work_dir/close.min.png" &&
     sips -g format -g pixelWidth -g pixelHeight "$work_dir/close@3x.png" "$work_dir/close.min.png"
   ```

4. 仅在 CLI 成功后导入。核对两份文件均为 PNG，像素宽高相同且对应设计的 3 倍导出尺寸。例如，24 × 24 pt 的切片应得到 72 × 72 px。缺少密钥、压缩失败或尺寸不符时，保留导出图并报告原因。
5. 将压缩结果以 `<资源名>@3x.png` 放入 `<资源名>.imageset`。在 `Contents.json` 的目标 `images` 条目填写该文件名及 `"scale": "3x"`。新建通用图片用 `"idiom": "universal"`，并添加 `"info": {"author": "xcode", "version": 1}`。更新已有资源时只改对应槽位，保留其他倍率、外观变体和渲染设置。新资源只提供 3x 文件，代码按资源名加载。[Apple Image Set 格式](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/ImageSetType.html)
