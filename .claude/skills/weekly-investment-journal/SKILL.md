---
name: weekly-investment-journal
description: 根据用户提供的本周操作和持仓列表，自动抓取市场数据、生成投资周记（【操作】【市场】【消息面】【持仓】四段式），保存到 投资/周记/ 目录。
user-invocable: true
allowed-tools:
  - WebSearch
  - mcp__playwright__browser_navigate
  - mcp__playwright__browser_evaluate
  - mcp__playwright__browser_snapshot
  - Read
  - Write
  - Glob
  - Edit
  - Bash(mkdir *)
---

# /weekly-investment-journal — 投资周记生成工作流

触发后，自动完成以下全流程，无需用户确认中间步骤。

Arguments passed: `$ARGUMENTS`

`$ARGUMENTS` 格式（用户提供）：
```
操作：XXX
持仓：股票A、股票B、ETF C、...
```

如果 `$ARGUMENTS` 为空，先向用户询问本周操作和持仓列表，再继续。

---

## Step 1：读取上期周记

用 Glob 找到 `投资/周记/` 目录下所有 `投资周记*.md`，按文件名排序，读取最新一期。

从上期周记中提取：
- 各持仓的投资逻辑摘要（用于本期持仓分析的背景）
- 上期宏观定性（用于判断本周变化方向）

---

## Step 2：抓取市场数据

用 Playwright 依次抓取以下数据（所有数据取当日收盘价）。

**A股指数**（东方财富，从页面 title 提取价格和涨跌幅）：
- 上证指数：`https://quote.eastmoney.com/zs000001.html`
- 深证成指：`https://quote.eastmoney.com/zs399001.html`
- 创业板指：`https://quote.eastmoney.com/zs399006.html`
- 科创50：`https://quote.eastmoney.com/zs000688.html`

**美股指数 / 美债 / 原油**（Yahoo Finance，用以下 JS 提取）：
```js
document.querySelector('section[data-testid="quote-hdr"]')?.textContent?.slice(0, 300)
```
- 标普500：`https://finance.yahoo.com/quote/%5EGSPC/`
- 纳指：`https://finance.yahoo.com/quote/%5EIXIC/`
- 10年期美债：`https://finance.yahoo.com/quote/%5ETNX/`
- 30年期美债：`https://finance.yahoo.com/quote/%5ETYX/`
- 布伦特原油：`https://finance.yahoo.com/quote/BZ=F/`

**人民币汇率**：从新浪财经首页抓取在岸人民币收盘价：
```js
Array.from(document.querySelectorAll('a')).find(a => a.textContent.includes('在岸人民币'))?.textContent
```

**持仓个股价格**（东方财富）：
- 对每个持仓标的，用东方财富行情页抓取当日收盘价和涨跌幅
- 代码规则：6开头用 `sh`，0/3开头用 `sz`
- URL格式：`https://quote.eastmoney.com/{sh/sz}{代码}.html`
- 从页面 title 提取价格（格式：`股票名 价格 涨跌(涨跌幅%)`）
- ETF 同上规则

---

## Step 3：抓取本周重要消息

访问 `https://finance.sina.com.cn/`，用以下 JS 抓取标题：

```js
Array.from(document.querySelectorAll('a')).filter(a => {
  const text = a.textContent.trim();
  return text.length > 10 && text.length < 60 &&
    (text.includes('美股') || text.includes('A股') || text.includes('美债') ||
     text.includes('关税') || text.includes('特朗普') || text.includes('美联储') ||
     text.includes('人民币') || text.includes('CPI') || text.includes('PPI') ||
     text.includes('降息') || text.includes('通胀') || text.includes('油价') ||
     text.includes('OPEC'));
}).slice(0, 20).map(a => a.textContent.trim())
```

从结果中筛选出本周真正重要的消息，最多保留 4-5 条，每条判断对持仓的影响方向。

---

## Step 4：生成周记

### 文件命名
格式：`投资周记 YYMMDD.md`，日期取今天。保存路径：`投资/周记/投资周记 YYMMDD.md`

### 文件结构（严格按此四段式）

```
投资周记

【操作】
{用户提供的操作描述，原文保留}

【市场】

{2-3句总括本周市场基调}

**A股**：截至{日期}收盘，上证{价格}（{涨跌幅}），深证成指{价格}（{涨跌幅}），创业板{价格}（{涨跌幅}），科创50 {价格}（{涨跌幅}）。{1句风格判断}

**美股**：标普500收{价格}（{涨跌幅}），纳指{价格}（{涨跌幅}）。{1句动能判断}

**美债**：10年期收益率{数值}%，30年期{数值}%。{1句对市场的影响}

**原油**：布伦特原油${价格}（{涨跌幅}）。{1句对持仓的影响}

**人民币**：在岸人民币收{汇率}。{1句影响判断}

【消息面】

{每条消息格式：**标题**：事件本身1句 + 对持仓的影响方向1句。只写真正重要的4-5条，不凑数。}

【持仓】

本周操作：{操作描述}

{每个持仓一段，格式：}
**{标的名} {价格}（{涨跌幅}）/{行业/品种}**：{本周表现1句} + {核心逻辑现状1-2句} + {操作结论或跟踪指标1句}
```

### 写作原则
- 【市场】只写数据和直接影响市场走向的事件，不重复【消息面】内容
- 【消息面】只写影响持仓逻辑但不一定反映在本周股价上的信息
- 【持仓】每个标的都要有当日价格和涨跌幅，逻辑要结合本周消息面更新，不能只复制上期
- 涨跌超过3%的持仓要解释原因
- 如果持仓逻辑出现矛盾（如加仓但股价走弱、利好但价格不涨），如实写出，不粉饰
- 除息日标注 XD
- 语气：直接、克制、有判断，不用"我认为"，不用感叹号

---

## Step 5：保存文件

将生成的周记写入 `投资/周记/投资周记 YYMMDD.md`。

完成后告知用户文件路径，并简要说明本周最值得关注的 1-2 个持仓变化。
