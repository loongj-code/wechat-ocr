# 微信 OCR

ZTools 插件，用于通过本地微信 OCR 运行时识别图片文字。支持 macOS 与 Windows x64。

> 本插件基于 [ZToolsCenter/ZTools-plugins](https://github.com/ZToolsCenter/ZTools-plugins) 中的 `wechat-ocr` 改造而来，主要变化是 Windows 端改为加载 [`@xiaojia5/wechat-ocr-native-win32`](../wechat-ocr-native-win32/)（koffi FFI），不再随包分发 `wxocr.dll`。

## 功能

- 选图、拖拽、粘贴截图三种方式输入
- 调用本地微信 OCR 运行时，返回识别文本与按行结构
- 首次进入界面自动检查 / 下载 OCR 运行时（macOS 拉取微信 OCR 动态库与模型；Windows 仅拉取 koffi 适配层和 `wcocr.dll`）

## 运行时

插件包不再内置较大的 `ocr-runtime` 文件。进入界面时会检查本地是否已安装 OCR 运行时，如果缺失或版本落后，会从 npmmirror 获取对应平台 runtime 包的最新版本并下载 tgz。

下载完成后，插件会解包 tgz 中的 `package/dist` 目录，并把其中内容作为本地 OCR 运行时缓存使用。

### 平台与 npm 包

| 平台 | npm 包 | 缓存目录 |
|------|--------|----------|
| macOS | `@ztools-center/wechat-ocr-native` | `~/Library/Application Support/ZTools/wechat-ocr/ocr-runtime` |
| Windows x64 | `@xiaojia5/wechat-ocr-native-win32` | `%APPDATA%\ZTools\wechat-ocr\ocr-runtime` |

Windows 运行时仅打包 koffi FFI 适配层与 `vendor/win32-x64/wcocr.dll`；OCR 引擎本体（`wxocr.dll`）从用户本机已安装的 Weixin 4.x 自动发现，**不**随插件分发。详细的发现路径与环境变量见 [`wechat-ocr-native-win32` 的 README](../wechat-ocr-native-win32/README.zh-CN.md)。

非 darwin/win32 系统调用插件接口会立即返回「当前系统暂不支持微信 OCR」；Windows 非 x64 架构返回「当前 Windows 架构暂不支持」。

## 故障排查

| 现象 | 可能原因 | 处置 |
|------|----------|------|
| 「未找到微信 OCR 引擎，请确认已安装 Weixin 4.x」 | Windows 端未安装 Weixin 4.x，或 OCR 插件尚未解压 | 安装并启动一次 Weixin 4.x；或在系统环境变量里设置 `WECHAT_OCR_EXE` |
| 「未找到微信安装目录（mmmojo_64.dll）」 | 注册表 `HKCU\Software\Tencent\Weixin /v InstallPath` 缺失 | 设置 `WECHAT_DIR` 指向 Weixin 版本目录 |
| 运行时下载失败 / 卡在「下载 OCR 运行时」 | npmmirror 不可访问或网络代理拦截 | 切换网络后重试；或手动下载对应平台 npm 包并解包到上表的缓存目录 |
| 想强制重置运行时 | 缓存版本错误或文件损坏 | 删除上表对应的 `ocr-runtime` 缓存目录后重启插件 |

## 开发

```bash
npm install
npm run dev
```

## 构建

```bash
npm run build
```

构建后，`README.md` 会复制到 `dist/README.md`。

## 许可与致谢

本插件代码采用 MIT 许可，详见 [LICENSE](./LICENSE)。

致谢：

- [ZToolsCenter/ZTools-plugins](https://github.com/ZToolsCenter/ZTools-plugins) — 原始 `wechat-ocr` 插件实现
- [`swigger/wechat-ocr`](https://github.com/swigger/wechat-ocr) — `wcocr.dll` C ABI 与 demo-7
- 微信 OCR 引擎（`wxocr.dll`）与模型属腾讯所有，遵循其各自许可，不随本插件分发
