# Lifeline

> 用一张专辑图，标记你的人生坐标。寻觅、收录、定格成章。

一个纯前端、零依赖的专辑海报生成器。搜索专辑、拖拽排序、选好画幅，一键导出你的 Top Albums 海报。

<p>
  <a href="https://limbkins.github.io/lifeline/">
    <img src="https://img.shields.io/badge/%E2%96%B6%20%E5%9C%A8%E7%BA%BF%E8%AF%95%E7%94%A8-%E7%82%B9%E6%AD%A4%E5%BC%80%E5%A7%8B-111111?style=for-the-badge&logo=github&logoColor=white" alt="在线试用" height="36">
  </a>
</p>

![单文件](https://img.shields.io/badge/single--file-HTML-e34f26?style=flat-square)
![无依赖](https://img.shields.io/badge/dependencies-0-4caf50?style=flat-square)
![主题](https://img.shields.io/badge/themes-3%20kinds-6b6b6b?style=flat-square)
![字体](https://img.shields.io/badge/fonts-9%20kinds-2c2218?style=flat-square)

---

## 这是什么

如果你在网易云、Spotify 或 Apple Music 里存过一堆喜欢的专辑，Lifeline 可以把它们排成一张海报：

搜到专辑 → 拖成你心里的名次 → 选画幅和背景 → 导出图片。

不用注册，不用登录，不联网也能打开（搜索需要联网）。列表存在你自己的浏览器里，不会上传到任何服务器。

<details>
<summary>点这里看效果长什么样</summary>

生成的成品是横竖各 2400px 的高清图，有两种排版：
- **阶梯排版** — 前排大、后排小，第 1 名最显眼
- **方形网格** — 正好 9 / 16 / 25 张时自动排成方阵

配色有三套：深邃黑、极简白、复古乐谱（羊皮纸配乐谱纹）。
标题字体 9 款可选，大小三档可调。

</details>

## 特性

- **专辑搜索** — 通过 iTunes Search API 检索专辑，实时返回封面
- **五档画幅** — 9 / 16 / 25 / 50 / 100 张，正方形数量自动生成正方形画布
- **阶梯式排版** — 前大后小，排名靠前的位置尺寸更大，权重一眼可辨
- **零空洞算法** — 数量不足时自动重算行列比例，不留空白格
- **手动补位** — 搜不到的专辑可本地上传图片，点空格精准填入对应名次
- **拖拽排序** — 长按进入移动模式，点目标格即可交换
- **背景墙** — 28 张精选专辑封面，四排反向无缝滚动，深色渐变蒙版压暗
- **背景主题** — 深邃黑 / 极简白 / 复古乐谱，切换后文字颜色自动适配
- **标题字体与字号** — 9 款中英文字体，标准 / 偏大 / 超大三档字号
- **即选即看** — 改主题、字体、字号、布局均即时重绘，无需重新生成
- **本地持久化** — 列表与设置存于 IndexedDB，关掉浏览器也不丢

## 快速开始

**方式一：直接用**

打开 👉 **https://limbkins.github.io/lifeline/**

无需安装，手机和电脑都能用。手机上生成后长按图片即可存入相册。

**方式二：下载到本地用**

无需构建、无需安装任何依赖：

```bash
git clone https://github.com/Limbkins/lifeline.git
cd lifeline
open index.html        # macOS
# 或直接双击 index.html
```

也可以起一个本地服务器（部分浏览器对 `file://` 更严格）：

```bash
python3 -m http.server 8000
# 访问 http://localhost:8000
```

## 使用说明

1. 在搜索框输入专辑名或艺人名，回车或点放大镜
2. 点搜索结果加入列表，可用「手动添加」上传本地图片
3. 点空格子 → 高亮 → 再点搜索结果，会精准填入该名次
4. 点已有封面可替换或删除；长按拖动可调整顺序
5. 选择数量档位，填写标题与作者，点「生成图片」导出海报

## 技术说明

整个应用是**一个 `index.html` 文件**，样式、逻辑、资源引用全部内联。

| 项目 | 说明 |
| --- | --- |
| 数据源 | [iTunes Search API](https://performance-partners.apple.com/search-api)（地区固定 `us`） |
| 存储 | IndexedDB（列表数据）、localStorage（偏好设置） |
| 渲染 | 原生 Canvas 2D 绘制海报 |
| 字体 | Fontsource CDN 按需加载，中文字库分片下载 |
| 依赖 | 无 |

### 几个实现细节

**`file://` 下必须走 JSONP。** 直接用 `fetch` 请求 iTunes 会被 CORS 拦截 —— 本地文件的 origin 是 `null`，而 iTunes 不返回 `access-control-allow-origin` 头。因此搜索走 JSONP（动态插入 `<script>`）而非 `fetch`。

**背景墙用 `collectionId` 直取。** 按专辑名搜索容易匹配到同名单曲、EP 或翻唱版本。这里预先解析出 28 个 `collectionId` 写死在数组里，用 `/lookup?id=a,b,c...` 批量取图，稳定且精确。

**封面按设备像素密度取图。** 按 `tile 实际渲染宽度 × devicePixelRatio` 计算所需物理像素，取不小于需求的最小档位（300 / 400 / 600），保证永远是缩小而非放大，画面锐利。图片由苹果 CDN 缓存，二次访问基本秒开。

**尊重系统的「减少动态效果」。** 系统开启该设置时背景墙暂停，并在底部提示一键启用 —— 照顾前庭功能障碍与晕动症用户，同时不让普通用户误以为页面坏了。

**复古乐谱底图内嵌为 Base64。** 本应用支持双击本地打开（`file://`），此时引用本地图片会污染 canvas，
`toDataURL` 直接抛 `SecurityError`，下载与长按保存会全部失效。内嵌 data URL 可彻底避开。
底图选用 WebP：同样 1200px 宽度，WebP 的 Base64 约 76KB，JPEG 需约 298KB。

**字体必须显式 `document.fonts.load()`。** `document.fonts.ready` 只等待「已开始的」加载，
而字体要等第一次真正绘制时才懒加载 —— 只等 `ready` 的话首帧一定用回退字体画完。
另外 `@font-face` 的 `font-style` 要写 `normal`：得意黑字形本身倾斜，若声明为 `oblique` 区间，
canvas 请求 `normal` 时会匹配失败并静默回退系统字体。

**标题变大只让画布变高。** 字号联动只改变画布高度与网格起始位置，
封面尺寸与每行列数的求解过程保持不变 —— 照片本身绝不缩放。

## 更新日志

见 [CHANGELOG.md](./CHANGELOG.md)。

## 浏览器支持

Chrome / Edge / Safari / Firefox 近两年版本。移动端已适配至 390px 宽度。

> 若背景墙静止不动：系统开启了「减少动态效果」。Windows 可在 *设置 → 辅助功能 → 视觉效果 → 动画效果* 中打开，或直接点页面底部的提示条。

## 许可

[MIT](./LICENSE)

## 致谢

设计与开发：[林布肯斯](https://github.com/Limbkins)

专辑封面版权归各唱片公司及艺人所有，本项目仅作展示用途。