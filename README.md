# Lifeline

> 用一张专辑图，标记你的人生坐标。寻觅、收录、定格成章。

一个纯前端、零依赖的专辑海报生成器。搜索专辑、拖拽排序、选好画幅，一键导出你的 Top Albums 海报。

![单文件](https://img.shields.io/badge/single--file-HTML-e34f26?style=flat-square)
![无依赖](https://img.shields.io/badge/dependencies-0-4caf50?style=flat-square)
![深色主题](https://img.shields.io/badge/theme-dark-111111?style=flat-square)

---

## 特性

- **专辑搜索** — 通过 iTunes Search API 检索专辑，实时返回封面
- **五档画幅** — 9 / 16 / 25 / 50 / 100 张，正方形数量自动生成正方形画布
- **阶梯式排版** — 前大后小，排名靠前的位置尺寸更大，权重一眼可辨
- **零空洞算法** — 数量不足时自动重算行列比例，不留空白格
- **手动补位** — 搜不到的专辑可本地上传图片，点空格精准填入对应名次
- **拖拽排序** — 长按进入移动模式，点目标格即可交换
- **背景墙** — 28 张精选专辑封面，四排反向无缝滚动，深色渐变蒙版压暗
- **本地持久化** — 列表与设置存于 IndexedDB，关掉浏览器也不丢

## 快速开始

无需构建、无需安装任何依赖：

```bash
git clone https://github.com/<your-name>/lifeline.git
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
| 依赖 | 无 |

### 几个实现细节

**`file://` 下必须走 JSONP。** 直接用 `fetch` 请求 iTunes 会被 CORS 拦截 —— 本地文件的 origin 是 `null`，而 iTunes 不返回 `access-control-allow-origin` 头。因此搜索走 JSONP（动态插入 `<script>`）而非 `fetch`。

**背景墙用 `collectionId` 直取。** 按专辑名搜索容易匹配到同名单曲、EP 或翻唱版本。这里预先解析出 28 个 `collectionId` 写死在数组里，用 `/lookup?id=a,b,c...` 批量取图，稳定且精确。

**封面按设备像素密度取图。** 按 `tile 实际渲染宽度 × devicePixelRatio` 计算所需物理像素，取不小于需求的最小档位（300 / 400 / 600），保证永远是缩小而非放大，画面锐利。图片由苹果 CDN 缓存，二次访问基本秒开。

**尊重系统的「减少动态效果」。** 系统开启该设置时背景墙暂停，并在底部提示一键启用 —— 照顾前庭功能障碍与晕动症用户，同时不让普通用户误以为页面坏了。

## 浏览器支持

Chrome / Edge / Safari / Firefox 近两年版本。移动端已适配至 390px 宽度。

> 若背景墙静止不动：系统开启了「减少动态效果」。Windows 可在 *设置 → 辅助功能 → 视觉效果 → 动画效果* 中打开，或直接点页面底部的提示条。

## 许可

[MIT](./LICENSE)

## 致谢

设计与开发：[林布肯斯](https://github.com/)

专辑封面版权归各唱片公司及艺人所有，本项目仅作展示用途。