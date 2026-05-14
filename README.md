# 微信 OCR

ZTools 插件，用于通过本地微信 OCR 运行时识别图片文字。支持 macOS 与 Windows x64。

## 运行时

插件包不再内置较大的 `ocr-runtime` 文件。进入界面时会检查本地是否已安装 OCR 运行时，如果缺失或版本落后，会从 npmmirror 获取对应平台 runtime 包的最新版本并下载 tgz。

下载完成后，插件会解包 tgz 中的 `package/dist` 目录，并把其中内容作为本地 OCR 运行时缓存使用。

### 平台与 npm 包

| 平台 | npm 包 | 缓存目录 |
|------|--------|----------|
| macOS | `@ztools-center/wechat-ocr-native` | `~/Library/Application Support/ZTools/wechat-ocr/ocr-runtime` |
| Windows x64 | `@xiaojia5/wechat-ocr-native-win32` | `%APPDATA%\ZTools\wechat-ocr\ocr-runtime` |

Windows 运行时仅打包 koffi FFI 适配层与 `vendor/win32-x64/wcocr.dll`；OCR 引擎本体（`wxocr.dll`）从用户本机已安装的 Weixin 4.x 自动发现，**不**随插件分发。如未安装 Weixin，识别会返回中文错误提示。

非 darwin/win32 系统调用插件接口会立即返回「当前系统暂不支持微信 OCR」；Windows 非 x64 架构返回「当前 Windows 架构暂不支持」。

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
