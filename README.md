# HYROX Sport Detail 查看器

## 项目简介

这是一个无后端、单文件的 HYROX 运动详情查看工具。用户可在浏览器中加载 `_sport_detail.json`（也接受 `.json`、`.txt`）文件，工具会在本地解析 `hyroxRepLap`、`hyroxCounting` 和 `hyroxTimelines` 三类数据，按协议字段顺序展示，并支持导出格式化 JSON 与 Excel。

仓库登记用途是将云端存储的 `hyroxreplap`、`hyroxcounting`、`hyroxtimelines` JSON 格式化成表格，供测试采集数据时提取关键信息；当前实现本身不读取云端地址，而是由用户选择或拖入本地文件。

## 核心功能

- 支持一次选择或拖拽多个 `.json` / `.txt` 文件，并在文件间切换查看。
- 解析可能以 JSON 字符串形式保存的 `hyroxRepLap` 与 `hyroxCounting`。
- 将 `hyroxTimelines` 的分号/逗号分隔字符串拆分为结构化记录。
- 按内置协议字段顺序展示三类数据，并过滤协议外未知字段。
- 将 RepLap 数据中的 `weight` 映射为 `equipment_weight`。
- 根据数组长度派生 `window_stroke_rates_count` 与 `group_durations_count`。
- 展示各类型记录数、列数等摘要信息。
- 分别下载三份格式化 JSON，或导出包含 `hyroxRepLap`、`hyroxCounting`、`hyroxTimelines` 三个 Sheet 的 `.xlsx` 文件。
- 数据解析、表格生成和文件导出均在浏览器本地完成，不上传业务数据。

## 适用场景

- 测试人员检查设备采集的 HYROX 运动详情数据。
- 开发人员核对三类 HYROX 结构的字段、条数与协议顺序。
- 将原始详情文件整理为便于筛选、对比和留档的 JSON 或 Excel。
- 批量浏览多个采集样本，并快速切换定位异常记录。

## 目录结构

```text
hyrox-detail-viewer/
├── index.html                         # 页面、样式、解析与导出逻辑
└── .github/
    └── workflows/
        └── feishu-notify.yml          # push 或手动触发的飞书通知工作流
```

## 使用方法

### 直接打开

1. 使用现代浏览器打开 `index.html`。
2. 点击上传区域，或将一个或多个 `.json` / `.txt` 文件拖入页面。
3. 在文件下拉框与三个数据页签之间切换，查看摘要和明细表格。
4. 点击“格式化 JSON”下载三份独立 JSON；点击“Excel (.xlsx)”下载三 Sheet 工作簿。

如浏览器限制本地文件页面行为，也可在仓库目录启动静态服务器：

```bash
python3 -m http.server 8000
```

随后访问 `http://localhost:8000/`。

## 输入与输出

### 主要输入字段

- `hyroxRepLap`：数组或可被 `JSON.parse` 的字符串。
- `hyroxCounting`：数组或可被 `JSON.parse` 的字符串。
- `hyroxTimelines`：以分号分隔记录、逗号分隔字段的字符串。

### 输出文件

- `<原文件名>.hyroxRepLap.formatted.json`
- `<原文件名>.hyroxCounting.formatted.json`
- `<原文件名>.hyroxTimelines.formatted.json`
- `<原文件名>.formatted.xlsx`

## 注意事项

- 页面只显示内置协议字段列表中、且输入记录实际包含的字段；未知字段会被过滤。
- `hyroxTimelines` 按固定字段顺序解析，输入顺序或分隔格式不一致会导致字段错位。
- 无法解析的 JSON 字符串字段会被当作空数组，文件顶层 JSON 无效时页面会报告解析失败。
- `equipment_weight`、两个 `*_count` 字段包含页面侧映射或派生逻辑，导出内容不一定与原始文件逐字一致。
- Excel 由页面内置的纯前端生成器创建，不依赖第三方表格库。
- 仓库没有独立构建脚本、包管理配置或自动化测试；核心实现集中在 `index.html`。
- GitHub Actions 工作流依赖仓库 Secrets `FEISHU_APP_ID`、`FEISHU_APP_SECRET`，并复用 `active-ailab/skills-manifest` 中的通知工作流；它不参与数据解析。
