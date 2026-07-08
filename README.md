# 超市进货数据中心 · 产品查询

一个纯静态网页，用来按产品名搜索你 Google Sheet 里的进货记录（支持多关键字、不区分大小写）。数据实时从 Google Sheet 读取，不需要后端服务器，可以直接部署在 GitHub Pages 上。

## 第一步：把 Google Sheet 发布为 CSV

1. 打开你的 Google Sheet「超市进货数据中心」。
2. 点击 **文件 (File) → 分享 (Share) → 发布到网络 (Publish to web)**。
3. 在弹出的窗口里：
   - 左边下拉框选择存放进货记录的那个具体分页（不要选"整个电子表格"），也就是包含 `product name`、`UPC code` 等列的那张表。
   - 右边格式选择 **逗号分隔值文件 (.csv)**。
4. 点击 **发布 (Publish)**，确认。
5. 复制得到的链接，形如：
   ```
   https://docs.google.com/spreadsheets/d/e/2PACX-xxxxxxxxxxxxxxxx/pub?gid=0&single=true&output=csv
   ```

> 注意：「发布到网络」和「共享权限」是两回事——即使 Sheet 本身没有对外共享，发布到网络后这个 CSV 链接也是任何人都能访问的只读数据。如果表格里有敏感信息（比如成本价），需要考虑清楚是否适合公开。

## 第二步：把链接填进网页

打开 `index.html`，找到这一行：

```js
const CSV_URL = "PASTE_YOUR_PUBLISHED_CSV_URL_HERE";
```

把 `PASTE_YOUR_PUBLISHED_CSV_URL_HERE` 换成你第一步拿到的链接，保存文件。

## 第三步：部署到 GitHub Pages

1. 在 GitHub 上新建一个仓库（比如 `product-lookup`）。
2. 把 `index.html` 上传到仓库根目录（`README.md` 也可以一起上传）。
3. 进入仓库 **Settings → Pages**。
4. Source 选择 `Deploy from a branch`，Branch 选择 `main` / `/ (root)`，保存。
5. 等待一两分钟，GitHub 会给你一个网址，形如：
   ```
   https://你的用户名.github.io/product-lookup/
   ```
6. 打开这个网址，就是你的产品查询网页了。

## 使用说明

- 在搜索框输入产品名关键字，比如 `森永` 或 `森永 草莓`。
- 支持多个关键字，用空格分隔，会同时匹配所有关键字（AND 逻辑），不区分大小写。
- 结果按到货日期从新到旧排列，同一产品多次进货会分别显示，方便看价格变化历史。
- 每次搜索都会重新读取一次表格数据，所以 Sheet 更新后网页会自动同步（可能有几分钟的 Google 缓存延迟）。

## 关于数据延迟

Google 对「发布到网络」的 CSV 通常有几分钟的缓存，Sheet 改了之后网页不一定立刻更新，一般等 1-5 分钟刷新页面即可看到最新数据。

## 想调整的地方

- **显示的字段**：在 `index.html` 的 `render()` 函数里，`meta-grid` 部分列出了到货日期、供应商、UPC、规格、成本、发票号，可以按需增删。
- **搜索范围**：目前只匹配「产品名」列（`COLS.productName`）。如果想同时搜 UPC 或供应商，可以在 `search()` 函数里把匹配范围扩大。
- **配色/样式**：都在 `<style>` 里的 CSS 变量（`--paper`、`--stamp`、`--seal` 等）里，改这几个颜色即可整体换风格。
