---
name: lixinger-universal
display_name: 理杏仁通用金融数据查询 Skill
version: 2.0.0
description: >
  理杏仁全量 Open API 智能查询 Skill。支持 A股/指数/基金全品类金融数据：
  股票基本面、K线行情、财务报表、估值分位、分红、增减持、股东结构、营收构成、
  公告、热度、资金流向等 50+ 接口。自然语言输入，自动意图解析，自动接口匹配，
  零门槛、免代码，普通投资者直接使用。
author: WorkBuddy Skill Architect
keywords:
  - 理杏仁
  - lixinger
  - A股数据
  - 股票财务
  - 估值分位
  - 基本面
  - K线
  - 分红
  - 增减持
  - 营收构成
  - 财务报表
  - 金融数据
  - 上市公司
  - 股东结构
trigger_phrases:
  - 理杏仁
  - lixinger
  - 查询.*财务
  - 查询.*估值
  - 查询.*K线
  - 查询.*分红
  - 查询.*增减持
  - 查询.*营收构成
  - 查询.*股东
  - 查询.*PE
  - 查询.*PB
  - 查询.*ROE
  - 理杏仁数据
  - lx数据
  - lx接口
applicable_scenarios:
  - A股股票基本面研究
  - 量化选股数据获取
  - 财务报表分析
  - 估值历史分位查询
  - 增减持动向追踪
  - 分红历史研究
  - 营收构成分析
  - 股东结构监控
  - K线行情数据
  - 上市公司公告查询
security:
  token_storage: private_config
  token_hardcoded: false
  data_policy: readonly_api_only
  no_fabrication: true
---

# 理杏仁通用金融数据查询 Skill v2.0

> **理杏仁，用数据认知世界，独立思考，理性投资。**
> 本 Skill 全量覆盖理杏仁开放平台 50+ 接口，自然语言驱动，自动匹配 API，零门槛配置，全会员可用。

---

## ⚡ 30 秒快速开始

**第一步**：获取你的理杏仁 Token → [https://www.lixinger.com/open/api](https://www.lixinger.com/open/api)（登录 → 个人中心 → API 管理 → 创建 Token → 复制）

**第二步**：告诉 AI 你的 Token：
```
设置理杏仁 Token：your_token_here
```

**第三步**：直接提问：
```
查询贵州茅台最近5年的PE估值分位
查询迈瑞医疗2020-2024年营收和净利润
查询宁德时代近3年分红情况
```

---

## 🔐 Token 配置规范（安全基线）

### 存储规则（强制）
- Token 存储位置：`~/.workbuddy/skills/lixinger-universal/config.json`
- **禁止**将 Token 硬编码到任何脚本、代码或响应正文
- **禁止**将 Token 打印输出到日志或对话界面
- 每位会员的 Token **独立私有**，互不共享、互不可见

### Token 快捷管理指令

| 指令 | 功能 |
|------|------|
| `设置理杏仁 Token：xxx` | 保存新 Token |
| `更新理杏仁 Token：xxx` | 覆盖更新 Token |
| `重置理杏仁 Token` | 清空 Token |
| `查看理杏仁 Token 状态` | 显示过期时间（不显示明文） |

### Token 读取方式（AI 执行代码标准模板）

```python
import json, os

def load_token() -> str:
    """安全读取 Token，不输出明文"""
    config_path = os.path.expanduser(
        "~/.workbuddy/skills/lixinger-universal/config.json"
    )
    if not os.path.exists(config_path):
        raise FileNotFoundError(
            "未找到 Token 配置。请先执行：设置理杏仁 Token：your_token_here\n"
            "Token 获取地址：https://www.lixinger.com/open/api"
        )
    with open(config_path, encoding="utf-8") as f:
        cfg = json.load(f)
    token = cfg.get("token", "").strip()
    if not token:
        raise ValueError(
            "Token 为空，请重新配置：设置理杏仁 Token：your_token_here"
        )
    return token
```

---

## 🔄 标准执行流程（顺序锁死，不可跳步）

```
Step 1  意图精准解析
         ├─ 提取：标的名称/代码（如 贵州茅台/600519）
         ├─ 提取：查询类型（估值/财报/行情/分红/股东/营收构成…）
         ├─ 提取：时间周期（日/周/月/季/年/具体日期区间）
         └─ 提取：具体指标（PE/PB/ROE/营收/净利润…）

Step 2  智能接口匹配
         ├─ 查阅内置接口映射表（见下方"全量 API 接口总表"）
         └─ 自动选择最合适的接口 endpoint

Step 3  参数校验与智能补全
         ├─ 必填参数齐全 → 直接进入 Step 4
         └─ 缺少关键信息 → 友好追问（见"缺参提示模板"）

Step 4  Token 读取与合规检测
         ├─ 自动读取 config.json 中的 Token
         └─ Token 缺失/过期 → 触发配置引导提示

Step 5  标准化 API 请求
         ├─ 调用 scripts/lixinger_universal.py
         ├─ 统一封装：Content-Type: application/json，POST 方式
         ├─ 超时配置：60 秒（网络慢时自动重试 1 次）
         └─ 全链路异常捕获（Token 失效/网络超时/接口报错/参数错误）

Step 6  结果解析与格式化输出
         ├─ 结构化解析 API 返回 JSON
         ├─ 优先表格可视化呈现核心数据
         ├─ 附带通俗化专业解读（专业术语 + 白话说明）
         └─ 异常场景：标准友好提示 + 解决方案指引
```

---

## 🗺️ 全量 API 接口总表（智能匹配依据）

### 📌 接口基础规范

| 项目 | 规范 |
|------|------|
| **基础域名** | `https://open.lixinger.com/api`（固定，严禁变更） |
| **请求方式** | 全部 POST |
| **Content-Type** | `application/json` |
| **鉴权字段** | 请求体中的 `token` 字段（自动注入，无需手动） |
| **股票代码格式** | 纯数字（如 `600519`），**禁止**带 `.SH`/`.SZ`/`.BJ` 后缀 |
| **日期格式** | `YYYY-MM-DD` |

---

### 一、公司基础信息

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 公司信息/基本信息 | `company` | `/cn/company` | `stockCodes[]` | 股票详细基础信息 |
| 公司概况/简介 | `profile` | `/cn/company/profile` | `stockCode` | 公司概况一句话简介 |
| 股本变动 | `equity_change` | `/cn/company/equity-change` | `stockCodes[]` | 股本结构历史变动 |
| K线/行情/股价 | `candlestick` | `/cn/company/candlestick` | `stockCode` | OHLCV K线数据（支持前复权/后复权） |
| 股东人数 | `shareholders_num` | `/cn/company/shareholders-num` | `stockCode` | 历史股东人数变化 |

**K线接口 extra 参数**：
```json
{"type": "qfq"}   // 前复权（推荐）
{"type": "hfq"}   // 后复权
{"type": "bfq"}   // 不复权
```

---

### 二、增减持动向

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 高管增减持 | `exec_shares_change` | `/cn/company/senior-executive-shares-change` | `stockCode` | 高管持股变动 |
| 大股东增减持 | `major_shares_change` | `/cn/company/major-shareholders-shares-change` | `stockCode` | 大股东持股变动 |

**返回字段说明**：
- 高管：`shareholderName`（姓名）、`duty`（职务）、`date`（日期）、`changedShares`（变动股数）、`avgPrice`（均价）、`changeReason`（原因）
- 大股东：`shareholderName`（名称）、`date`（日期）、`changeQuantity`（变动股数）、`sharesChangeRatio`（变动比例）、`avgPrice`（均价）

⚠️ **注意**：央企高管持股通常不公开披露，返回空数组属正常现象。

---

### 三、交易数据

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 龙虎榜 | `trading_abnormal` | `/cn/company/trading-abnormal` | `stockCode` | 龙虎榜异常交易 |
| 大宗交易 | `block_deal` | `/cn/company/block-deal` | `stockCode` | 大宗交易记录 |
| 股权质押 | `pledge` | `/cn/company/pledge` | `stockCode` | 股权质押情况 |

---

### 四、经营数据

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 营收构成/收入结构 | `revenue_constitution` | `/cn/company/operation-revenue-constitution` | `stockCodes[]` | 分产品/地区营收构成 |
| 经营数据 | `operating_data` | `/cn/company/operating-data` | `stockCodes[]` | 公司自定义经营指标 |

---

### 五、归属信息

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 所属指数 | `indices` | `/cn/company/indices` | `stockCodes[]` | 股票归属哪些指数 |
| 所属行业 | `industries` | `/cn/company/industries` | `stockCodes[]` | 行业分类归属 |

---

### 六、公告与监管

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 公告/年报/季报 | `announcement` | `/cn/company/announcement` | `stockCode` | 历史公告列表 |
| 监管措施 | `measures` | `/cn/company/measures` | `stockCodes[]` | 监管处罚记录 |
| 问询函 | `inquiry` | `/cn/company/inquiry` | `stockCodes[]` | 交易所问询记录 |

---

### 七、股东信息

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 前十大股东/主要股东 | `majority_shareholders` | `/cn/company/majority-shareholders` | `stockCode` | 前十大股东持股 |
| 前十大流通股东 | `nolimit_shareholders` | `/cn/company/nolimit-shareholders` | `stockCode` | 前十大流通股东 |
| 公募基金持股 | `fund_shareholders` | `/cn/company/fund-shareholders` | `stockCode` | 基金持股情况 |
| 基金公司持股 | `fund_collection_shareholders` | `/cn/company/fund-collection-shareholders` | `stockCode` | 基金公司汇总持股 |

---

### 八、分红与配股

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 分红/股息 | `dividend` | `/cn/company/dividend` | `stockCode` | 历史分红记录 |
| 配股 | `allotment` | `/cn/company/allotment` | `stockCode` | 历史配股记录 |

---

### 九、客户与供应商

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 说明 |
|---------|---------|---------|---------|------|
| 客户/前五大客户 | `customers` | `/cn/company/customers` | `stockCodes[]` | 主要客户集中度 |
| 供应商/前五大供应商 | `suppliers` | `/cn/company/suppliers` | `stockCodes[]` | 主要供应商集中度 |

---

### 十、基本面估值（PE/PB/PS 等）

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 适用对象 |
|---------|---------|---------|---------|---------|
| PE/PB/PS/估值 | `fundamental` | `/cn/company/fundamental/non_financial` | `stockCodes[]` | 非金融股（绝大多数A股） |
| 银行估值 | `fundamental_bank` | `/cn/company/fundamental/bank` | `stockCodes[]` | 银行股 |
| 证券估值 | `fundamental_security` | `/cn/company/fundamental/security` | `stockCodes[]` | 证券股 |
| 保险估值 | `fundamental_insurance` | `/cn/company/fundamental/insurance` | `stockCodes[]` | 保险股 |
| 其他金融估值 | `fundamental_other_fin` | `/cn/company/fundamental/other_financial` | `stockCodes[]` | 其他金融 |

**常用 metricsList 字段**（估值接口）：

```
估值倍数（无前缀，直接使用）：
pe_ttm          市盈率（TTM）
d_pe_ttm        市盈率（TTM，扣非）
pb              市净率
pb_wo_gw        市净率（不含商誉）
ps_ttm          市销率（TTM）
pcf_ttm         市现率（TTM）
dyr             股息率
ev_ebit_r       EV/EBIT
ev_ebitda_r     EV/EBITDA
ey              公司收益率

历史分位点（格式：指标名.年限.cvpos）：
pe_ttm.y5.cvpos      PE-TTM 5年分位
pe_ttm.y10.cvpos     PE-TTM 10年分位
d_pe_ttm.y5.cvpos    扣非PE 5年分位
d_pe_ttm.y10.cvpos   扣非PE 10年分位
pb.y5.cvpos          PB 5年分位
pb.y10.cvpos         PB 10年分位
pb_wo_gw.y5.cvpos    PB（不含商誉）5年分位
pb_wo_gw.y10.cvpos   PB（不含商誉）10年分位

statisticsDataType：
cvpos  分位点百分比（0%=历史最低，100%=历史最高，越低越低估）
minv   历史最低值
maxv   历史最高值
avgv   历史平均值
```

⚠️ **重要**：`market_cap`（市值）不在 fundamental 接口，需通过 company 接口获取。

---

### 十一、财务报表

| AI 别名 | 接口键名 | 接口路径 | 股票参数 | 适用对象 |
|---------|---------|---------|---------|---------|
| 财务报表/财务数据 | `fs` | `/cn/company/fs/non_financial` | `stockCodes[]` | 非金融股 |
| 银行财报 | `fs_bank` | `/cn/company/fs/bank` | `stockCodes[]` | 银行股 |
| 证券财报 | `fs_security` | `/cn/company/fs/security` | `stockCodes[]` | 证券股 |
| 保险财报 | `fs_insurance` | `/cn/company/fs/insurance` | `stockCodes[]` | 保险股 |
| 其他金融财报 | `fs_other_fin` | `/cn/company/fs/other_financial` | `stockCodes[]` | 其他金融 |

**metricsList 指标名格式**：`[粒度].[分类].[指标名].[计算类型]`

例：`y.ps.toi.t`（年度/利润表/营业收入/当期值）

**粒度前缀**：
- `y.`  → 年度
- `hy.` → 半年度
- `q.`  → 季度

**计算类型后缀**：
- `.t`    → 当期值
- `.t_y2y` → 同比变化率
- `.ttm`  → 近12个月滚动（利润表/现金流量表专用）
- `.c`    → 累积值（利润表/现金流量表，季/半年专用）

---

#### 📊 利润表（ps 分类）常用指标

```
y.ps.toi.t              营业总收入（当期）
y.ps.toi.t_y2y          营业收入同比增速
y.ps.cp.t               营业成本
y.ps.gp.t               毛利润
y.ps.np.t               净利润
y.ps.npadnrpatoshaopc.t 归母净利润（归属母公司股东净利润）
y.ps.ope.t              营业利润
y.ps.tp.t               利润总额
```

#### 📊 资产负债表（bs 分类）常用指标

```
y.bs.ta.t               总资产
y.bs.tca.t              流动资产合计
y.bs.cabb.t             货币资金
y.bs.ar.t               应收账款
y.bs.i.t                存货
y.bs.tl.t               负债合计
y.bs.lwi.t              有息负债
y.bs.stl.t              短期借款
y.bs.lte.t              长期借款
y.bs.tetoshopc.t        归属母公司股东权益
y.bs.gw.t               商誉
```

#### 📊 现金流量表（cfs 分类）常用指标

```
y.cfs.ncffoa.t          经营活动现金流量净额（经营OCF）
y.cfs.ncffia.t          投资活动现金流量净额
y.cfs.ncfffa.t          筹资活动现金流量净额
```

#### 📊 管理指标（m 分类）——盈利/偿债/运营全覆盖

```
盈利能力：
y.m.gp_m.t              毛利率（⚠ 返回小数，如0.55=55%，展示需×100）
y.m.np_s_r.t            净利率（销售净利率）
y.m.wroe.t              加权平均ROE

偿债能力（⚠ 仅年度粒度有效，季度返回None）：
y.m.tl_ta_r.t           资产负债率
y.m.lwi_ta_r.t          有息负债率
y.m.c_r.t               流动比率
y.m.q_r.t               速动比率
y.m.lv_r.t              清算价值比率
y.m.fa_ta_r.t           固定资产占总资产比率

运营能力：
y.m.ta_to.t             总资产周转率
y.m.ar_ds.t             应收账款周转天数
y.m.ap_ds.t             应付账款周转天数
y.m.i_ds.t              存货周转天数

现金流质量：
y.m.fcf.t               自由现金流量
y.m.ncffoa_np_r.t       净现比（经营OCF/净利润）
y.m.ncffoa_op_r.t       经营OCF/营业利润比率
y.m.crfscapls_oi_r.t    销售收现比
```

---

### 十二、热度数据

| AI 别名 | 接口键名 | 接口路径 | 说明 |
|---------|---------|---------|------|
| 分红再投入收益率 | `hot_tr_dri` | `/cn/company/hot/tr_dri` | 含分红再投入的总收益率 |
| 互联互通热度 | `hot_mm_ha` | `/cn/company/hot/mm_ha` | 北向/南向资金热度 |
| 融资融券热度 | `hot_mtasl` | `/cn/company/hot/mtasl` | 融资融券余额热度 |
| 高管增减持热度 | `hot_esc` | `/cn/company/hot/esc` | 高管增减持热度指标 |
| 大股东增减持热度 | `hot_mssc` | `/cn/company/hot/mssc` | 大股东增减持热度 |
| 龙虎榜热度 | `hot_t_a` | `/cn/company/hot/t_a` | 龙虎榜出现频次 |
| 限售解禁 | `hot_elr` | `/cn/company/hot/elr` | 限售股解禁计划 |
| 股权质押热度 | `hot_ple` | `/cn/company/hot/ple` | 股权质押比率热度 |
| 人均指标 | `hot_capita` | `/cn/company/hot/capita` | 人均持股、人均市值等 |
| 股东人数变化 | `hot_shnc` | `/cn/company/hot/shnc` | 股东人数增减变化趋势 |
| 分红融资 | `hot_df` | `/cn/company/hot/df` | 分红与融资对比 |
| 派息热度 | `hot_npd` | `/cn/company/hot/npd` | 派息金额热度 |
| 换手率 | `hot_tr` | `/cn/company/hot/tr` | 历史换手率数据 |

---

### 十三、资金流向

| AI 别名 | 接口键名 | 接口路径 | 说明 |
|---------|---------|---------|------|
| 互联互通/北向资金 | `mutual_market` | `/cn/company/mutual-market` | 沪深港通资金流向 |
| 融资融券 | `margin_trading` | `/cn/company/margin-trading-and-securities-lending` | 融资买入/融券卖出数据 |

---

## ⚙️ 参数规范速查

### stockCode vs stockCodes（⚠️ 高频易错）

| 参数名 | 类型 | 适用接口 |
|--------|------|---------|
| `stockCode`（单数）| `string` | candlestick、profile、shareholders_num、pledge、majority_shareholders、nolimit_shareholders、fund_shareholders、fund_collection_shareholders、dividend、allotment、announcement、exec_shares_change、major_shares_change、trading_abnormal、block_deal |
| `stockCodes`（复数）| `string[]` | company、equity_change、operating_data、revenue_constitution、indices、industries、measures、inquiry、customers、suppliers、fundamental*、fs*、hot_*、mutual_market、margin_trading |

### 智能股票代码标准化

```python
def normalize_code(code: str) -> str:
    """自动去除交易所后缀，转为纯数字"""
    code = code.strip().upper()
    for suffix in [".SH", ".SZ", ".BJ", ".SS", ".SZE"]:
        if code.endswith(suffix):
            return code[: -len(suffix)]
    # 处理 sh/sz 前缀格式（如 sh600519）
    if code[:2] in ("SH", "SZ", "BJ"):
        return code[2:]
    return code
```

---

## 📋 自然语言 → 接口 智能映射规则

当用户提问时，AI 按以下规则自动选择接口：

| 用户说的关键词 | 自动选择接口 |
|--------------|------------|
| PE、PB、PS、市盈率、市净率、估值、分位 | `fundamental`（非金融）|
| 财务报表、营收、净利润、ROE、毛利率、资产负债率 | `fs` |
| K线、股价、行情、收盘价、开盘价、成交量 | `candlestick` |
| 分红、股息、派息 | `dividend` |
| 增减持、高管买卖、大股东减持 | `exec_shares_change` + `major_shares_change` |
| 股东、十大股东、机构持股 | `majority_shareholders` + `nolimit_shareholders` |
| 营收构成、收入结构、分产品 | `revenue_constitution` |
| 公告、年报披露、季报 | `announcement` |
| 北向资金、互联互通 | `mutual_market` |
| 融资融券 | `margin_trading` |
| 龙虎榜 | `trading_abnormal` |
| 大宗交易 | `block_deal` |
| 换手率 | `hot_tr` |
| 公司简介、基本信息 | `profile` + `company` |
| 限售解禁 | `hot_elr` |
| 股权质押 | `pledge` |

**金融股特殊处理规则**：
- 银行（招商银行/工商银行/建设银行 等） → 使用 `fundamental_bank` / `fs_bank`
- 证券（中信证券/国泰君安 等）→ 使用 `fundamental_security` / `fs_security`
- 保险（中国平安/中国人寿 等）→ 使用 `fundamental_insurance` / `fs_insurance`

---

## 🔢 关键数据格式转换规则

| 数据类型 | 理杏仁返回格式 | 展示格式 |
|---------|-------------|---------|
| 比率类（毛利率/资产负债率等） | 小数（如 0.55）| 百分比（55%），乘以 100 |
| 分位点 | 小数（如 0.35）| 百分比（35%），乘以 100 |
| 变动股数 | 整数（如 -3900000）| 万股（-390万股），除以 10000 |
| 金额 | 元（如 1000000000）| 亿元（10亿元），除以 100000000 |
| 日期 | ISO 8601 字符串 | YYYY-MM-DD |

---

## ❌ 标准化错误提示与处理

### Token 未配置
```
⚠️ 未检测到理杏仁 Token 配置。

请先获取你的私有 Token：
1. 访问 https://www.lixinger.com/open/api
2. 登录账号 → 个人中心 → API 管理
3. 创建 Token（免费，实名认证后可创建）
4. 复制 Token 后，对我说：

   设置理杏仁 Token：粘贴你的Token

配置完成后即可直接查询数据。
```

### Token 过期
```
⚠️ 理杏仁 Token 已过期，接口返回 401 鉴权错误。

请重新生成 Token：
1. 访问 https://www.lixinger.com/open/api
2. 进入 API 管理，重新创建 Token
3. 对我说：更新理杏仁 Token：新的Token

当前过期 Token 将被替换，其他数据不受影响。
```

### 接口超时
```
⚠️ 理杏仁接口响应超时（60秒内无响应）。

可能原因：
• 理杏仁服务器临时繁忙
• 网络连接不稳定

建议：稍等 30 秒后重试，或缩小查询时间范围再试。
```

### 接口限流（429）
```
⚠️ 查询频率超出限制（理杏仁限制：每分钟 1000 次，每秒 32 次）。

系统将自动等待 10 秒后重试（第 1 次重试中...）。

若持续限流，请：
• 减少同时查询的股票数量
• 缩短查询时间范围
• 稍等片刻再重新查询
```

### 返回数据为空
```
ℹ️ 理杏仁返回数据为空，可能原因：

• 该股票在查询时间段内无数据
• 所选指标不适用于该股票类型（如金融股需用专用接口）
• 央企高管持股信息不公开披露（高管增减持接口常返回空数组）

建议：
• 检查时间范围是否正确
• 确认股票类型是否匹配接口（银行/证券/保险需用专用接口）
```

### 参数错误（400）
```
⚠️ 接口参数错误，理杏仁返回 400 错误。

常见原因：
• 指标名格式错误（财务报表指标必须用 y.ps.xxx.t 格式）
• 使用了不存在的指标名（如 d_pb 不存在，应用 pb_wo_gw）
• stockCode/stockCodes 参数名用错（注意单复数区别）

系统将自动检查参数并重试。
```

---

## 💬 多场景调用示范案例

### 案例 1：查询估值分位（最常用）
```
用户：查一下贵州茅台最近5年和10年的PE估值分位

AI执行：
  接口：fundamental（非金融）
  股票：600519
  指标：pe_ttm, pe_ttm.y5.cvpos, pe_ttm.y10.cvpos, d_pe_ttm, d_pe_ttm.y5.cvpos
  时间：近30天（取最新值）

输出示例：
  | 指标           | 当前值    | 5年分位 | 10年分位 | 解读 |
  |---------------|---------|--------|--------|------|
  | PE(TTM)       | 28.5×   | 35%    | 42%    | 历史中低估区间 |
  | 扣非PE(TTM)    | 29.2×   | 38%    | 44%    | 与PE接近，盈利质量高 |
```

### 案例 2：查询财务三表关键指标
```
用户：帮我查迈瑞医疗2020-2024年的营收、净利润、ROE和毛利率

AI执行：
  接口：fs（非金融财务）
  股票：300760
  指标：y.ps.toi.t, y.ps.npadnrpatoshaopc.t, y.m.wroe.t, y.m.gp_m.t
  时间：2020-01-01 至 2025-01-01

输出示例：
  | 年份  | 营收(亿) | 归母净利润(亿) | ROE   | 毛利率 |
  |------|--------|------------|-------|------|
  | 2024 | 350.2  | 90.5       | 22.1% | 67.8% |
  | 2023 | 323.6  | 88.9       | 23.4% | 66.2% |
  | ...  | ...    | ...        | ...   | ...  |
```

### 案例 3：查询分红历史
```
用户：宁德时代近5年分红情况怎么样？

AI执行：
  接口：dividend
  股票：300750
  时间：2019-01-01 至今

输出：历年分红记录（除权日、每股分红金额、分红总额、股息率等）
```

### 案例 4：查询增减持动向
```
用户：最近一年有没有大股东减持宁德时代？

AI执行：
  接口：major_shares_change
  股票：300750
  时间：2025-01-01 至今

输出：大股东增减持记录（股东名称、变动日期、变动股数、变动比例、均价）
```

### 案例 5：查询营收构成
```
用户：比亚迪的收入主要来自哪些业务？

AI执行：
  接口：revenue_constitution
  股票：002594
  时间：最近1年

输出：分产品/分地区营收构成及占比
```

### 案例 6：K 线数据获取
```
用户：下载贵州茅台最近半年的日K线数据（前复权）

AI执行：
  接口：candlestick
  股票：600519
  时间：2025-10-01 至今
  额外参数：{"type": "qfq"}

输出：日期、开盘、最高、最低、收盘、成交量 表格数据
```

---

## 🔒 API 调用频率管控规则

| 规则 | 说明 |
|------|------|
| **每秒上限** | 32 次请求（内置自动节流） |
| **每分钟上限** | 1000 次请求 |
| **429 处理** | 自动等待 10 秒重试 1 次；重试仍失败则提示用户稍后再试 |
| **批量建议** | 单次查询尽量将多个指标合并在一个 metricsList 中，减少请求次数 |
| **超时设置** | 单次请求超时 60 秒；超时后自动重试 1 次 |

---

## 🚀 执行脚本调用规范

AI 执行数据查询时，统一调用 `scripts/lixinger_universal.py`：

```bash
# 基本用法
python ~/.workbuddy/skills/lixinger-universal/scripts/lixinger_universal.py \
  --api fundamental \
  --stock 600519 \
  --metrics pe_ttm,pe_ttm.y5.cvpos,pe_ttm.y10.cvpos \
  --start 2025-04-01 \
  --end 2025-04-30

# K线查询（前复权）
python ~/.workbuddy/skills/lixinger-universal/scripts/lixinger_universal.py \
  --api candlestick \
  --stock 600519 \
  --start 2025-01-01 \
  --end 2025-04-30 \
  --extra '{"type":"qfq"}'

# 财务报表多指标批量查询
python ~/.workbuddy/skills/lixinger-universal/scripts/lixinger_universal.py \
  --api fs \
  --stock 300760 \
  --start 2020-01-01 \
  --end 2025-01-01 \
  --metrics y.ps.toi.t,y.ps.npadnrpatoshaopc.t,y.m.wroe.t,y.m.gp_m.t

# 列出所有接口
python ~/.workbuddy/skills/lixinger-universal/scripts/lixinger_universal.py --list
```

**脚本自动处理**：
- Token 自动从 `config.json` 读取（无需 `--token` 参数）
- 股票代码自动标准化（去除后缀）
- 自动区分 stockCode/stockCodes
- 自动处理 429 限流（等待重试）
- 输出默认 pretty JSON，可用 `--format table` 输出表格

---

## 📌 高频踩坑总结（必读）

| 场景 | 错误做法 | 正确做法 |
|------|---------|---------|
| 查 PB（不含商誉） | 用 `d_pb` | 用 `pb_wo_gw` |
| 查财务指标 | 用 `revenue`、`net_profit` 等简写 | 用 `y.ps.toi.t` 等完整格式 |
| 偿债指标（流动比率等） | 用季度粒度 `q.m.c_r.t` | 用年度粒度 `y.m.c_r.t` |
| 金融股财务/估值 | 用 `fs`/`fundamental` | 用 `fs_bank`/`fundamental_bank` 等专用接口 |
| 分红接口 | 用 `stockCodes` 复数 | 用 `stockCode` 单数 |
| 增减持接口 | 用 `stockCodes` 复数 | 用 `stockCode` 单数 |
| 百分比展示 | 直接用理杏仁返回值（如 0.55）| 乘以 100（显示 55%）|
| 市值查询 | 用 fundamental 接口查 market_cap | market_cap 不在 fundamental，用 company 接口 |

---

## 📎 数据声明

> 本 Skill 所有数据均来自理杏仁开放平台（lixinger.com），100% 沿用原始 API 返回数据，不编造、不补全、不臆造任何金融数据。无数据时如实告知，不做 AI 幻觉填充。
>
> 理杏仁数据仅供参考，不构成任何投资建议。投资有风险，入市需谨慎。

---

*接口过期时间：以用户配置的 Token 为准，请定期至理杏仁官网更新。*
