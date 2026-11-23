[README.md](https://github.com/user-attachments/files/35251292/README.md)
# 谷歌SEO市场和关键词分析 · Google SEO Market & Keyword Analysis

一个 WorkBuddy / Agent Skill：把「一个产品」变成「一张可直接执行的关键词作战地图」。

从 Google、Bing、Amazon、Reddit、YouTube、DuckDuckGo 采集真实联想词与社区讨论，输出词族分类、搜索意图、KD 估算、A/B/C/D 优先级矩阵、话题清单和内容排期表。

---

## 它解决什么问题

做谷歌 SEO 最耗时的不是写文章，而是**决定写什么**。大多数人的做法是凭感觉挑词，结果写了十篇没有一篇有排名。

这个 Skill 把这件事拆成可复现的六步：

1. **问清 4 件事** — 产品（英文品类词）、市场语言、代理、平台
2. **采集真实词** — 联想词接口 + SERP，不凭记忆编词
3. **清洗归族** — 剔除品牌词和同名不同物，按 SERP 重叠度分族
4. **标注意图** — 信息型 / 商业调研 / 交易型 / 导航型
5. **打分排序** — 搜索量三信号估算 + KD 十项启发式 → A/B/C/D
6. **产出交付** — Markdown / HTML / Excel 三件套 + 12 个月排期

## 核心特点

- **不编数据**：搜索量一律标注为估算区间并写明推导依据，不假装是 Ahrefs 精确值
- **代理可选**：有代理跑全平台；无代理用 Bing + Amazon + WebSearch 同样能出结果
- **实测过的接口**：7 个平台接口的连通性、参数、返回格式都实测记录在 `references/platform-endpoints.md`
- **能识别陷阱**：`silicone hot water bottle`（热水袋）≠ `silicone water bottle`（水瓶），这类同名不同物会被自动标 D 级
- **B2B 优先**：`manufacturer` / `OEM` / `wholesale` 类词量小但 KD 低、询盘质量高，规则里强制给 A 级

## 安装

### 方式一：WorkBuddy 客户端（自用）

1. 下载本仓库，或直接使用 `google-seo-market-keyword-analysis.zip`
2. 解压到用户级技能目录：
   - Windows：`%USERPROFILE%\.workbuddy-ai\skills\`
   - macOS / Linux：`~/.workbuddy-ai/skills/`
3. 确认结构为 `skills/google-seo-market-keyword-analysis/SKILL.md`
4. 重启客户端，在「技能」列表里启用

### 方式二：项目级（随仓库共享）

把整个目录放到项目的 `.workbuddy/skills/` 下，提交到 git，团队成员拉取即生效。

## 使用

直接说人话即可触发：

```
查一下硅胶水瓶的关键词和热度，做成谷歌SEO文章规划
帮我分析 silicone trivet 这个品类值不值得做
把这份关键词表导出 excel
```

Skill 会先问你 4 个问题，然后开始采集。

### 手动跑脚本

```bash
# 1. 探测当前网络下哪些平台可达
python scripts/collect_suggest.py --probe --market US

# 2. 采集（有代理时全平台）
python scripts/collect_suggest.py \
  --seed "silicone water bottle" \
  --market US --lang en \
  --platforms google,bing,amazon,youtube,ddg,reddit \
  --proxy "http://127.0.0.1:7890" \
  --alphabet --modifiers \
  --out keywords_raw.json

# 3. 建 Excel（需先按 references/output-spec.md 写好 annotations.json）
python scripts/build_excel.py --annotations annotations.json --out 关键词分析.xlsx
```

依赖：Python 3.8+，`openpyxl`。socks5 代理需额外 `pip install requests pysocks`。

## 目录结构

```
google-seo-market-keyword-analysis/
├── SKILL.md                          # 主流程：4 项必问 + 6 步工作流
├── README.md
├── references/
│   ├── platform-endpoints.md         # 7 平台接口实测 + 12 国 Amazon mid 对照表
│   ├── scoring-model.md              # 搜索量估算 / KD 启发式 / 优先级矩阵 / 市场判断
│   └── output-spec.md                # annotations.json 契约 + Excel/MD/HTML 模板
└── scripts/
    ├── collect_suggest.py            # 多平台联想词采集（含 --probe）
    └── build_excel.py                # JSON → 5 工作表 xlsx
```

## 实测记录

`silicone water bottle` + Bing/Amazon + 修饰词扩展 → 32 次查询 → **158 个去重关键词，0 错误**。

本机网络连通性（中国，无代理）：

| 平台 | 直连 | 说明 |
|---|---|---|
| Amazon | ✅ | 0.3s 返 10 条，唯一可直连主力 |
| Bing | ✅ | 返 25 条，必须带浏览器 UA，否则返回空的 204 |
| Google / YouTube / DuckDuckGo / Reddit | ❌ | 超时，需 http 代理 |

## 许可

MIT License，见 `LICENSE`。
