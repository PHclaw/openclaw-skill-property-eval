# openclaw-skill-property-eval

## 简介

房产评估 + 投资分析 Skill。输入房产基本信息，输出市场估值、租金回报分析、投资回报预测。

## 触发场景

- "房产评估"、"property valuation"
- "投资分析"、"investment analysis"
- "租金回报"、"rental yield"
- "买房分析"、"home buying"
- "租售比"、"price-to-rent ratio"

## 核心流程

### 第一步：收集房产信息

```markdown
## 房产基本信息

### 基本信息
- 城市/区域：
- 小区名称：
- 建筑面积：XX㎡
- 户型：X室X厅X卫
- 楼层：X/XX层
- 朝向：南/南北通透
- 建成年代：
- 装修情况：

### 价格信息
- 挂牌价：XXX万
- 单价：XXX元/㎡
- 周边均价：XXX元/㎡

### 租金信息（如有）
- 当前租金：XXXX元/月
- 租约到期：

### 贷款（如有）
- 贷款金额：XXX万
- 利率：X.X%
- 月供：XXXX元
```

### 第二步：估值方法

**方法1：可比交易法（市场比较法）**

```python
def market_comparison(subject_price, subject_area,
                      comps: list[dict]) -> dict:
    """可比交易法估值"""
    # 计算参考单价
    comp_prices = [c['price_per_sqm'] for c in comps]
    median_price = sorted(comp_prices)[len(comp_prices)//2]
    estimated = median_price * subject_area

    # 调整系数
    adjustments = 0
    for c in comps:
        # 楼层：中间层 +2%，顶层 -3%，底层 -5%
        if c['floor'] == 'mid':
            adjustments += 0.02
        elif c['floor'] == 'top':
            adjustments -= 0.03
        elif c['floor'] == 'ground':
            adjustments -= 0.05
        # 朝向、装修、楼龄...

    adjusted = estimated * (1 + adjustments / len(comps))
    return {
        "estimated_price": round(adjusted / 10000, 1),  # 万
        "per_sqm": round(adjusted / subject_area),
        "vs_listing": round((adjusted - subject_price) / 10000, 1)
    }
```

**方法2：租金回报法**

```python
def rental_analysis(price: float, monthly_rent: float) -> dict:
    """租金回报分析"""
    gross_yield = (monthly_rent * 12) / price * 100
    # 净回报（扣除物业费、维护费、空置期）
    net_yield = gross_yield * 0.8  # 粗估扣20%
    # 租售比
    price_to_rent = price / (monthly_rent * 12)
    return {
        "gross_yield": f"{gross_yield:.2f}%",
        "net_yield": f"{net_yield:.2f}%",
        "price_to_rent_ratio": f"{price_to_rent:.1f}倍",
        "signal": "值得投资" if gross_yield > 5 else
                  "回报一般" if gross_yield > 3 else "回报偏低"
    }
```

### 第三步：投资回报预测

```markdown
## 💰 投资回报预测

### 基础假设
- 持有年限：5年
- 年均涨幅：3%（保守）/ 5%（中性）/ 8%（乐观）
- 租金年涨幅：2%
- 交易成本：房价的6-8%（中介费+税费）

### 5年回报预测

| 情景 | 房价涨幅 | 5年后估值 | 累计租金 | 总收益 | 年化回报 |
|------|---------|---------|---------|-------|---------|
| 保守 | 3%/年 | XXX万 | XX万 | XX万 | X.X% |
| 中性 | 5%/年 | XXX万 | XX万 | XX万 | X.X% |
| 乐观 | 8%/年 | XXX万 | XX万 | XX万 | X.X% |
```

### 第四步：输出格式

```
## 房产评估报告

### 基本信息
- 小区：XXXX
- 面积：XX㎡ | 单价：XXX元/㎡
- 挂牌价：XXX万

### 估值结果
| 方法 | 估值 | 与挂牌价差 |
|------|------|-----------|
| 可比交易法 | XXX万 | +X万 / -X万 |
| 租金回报法 | XXX万 | （参考） |

### 投资分析
- 租售比：XX倍（合理区间：15-25倍）
- 毛租金回报率：X%
- 月供/租金比：X.X（>1.5 压力较大）
- 综合评价：值得购入 / 谨慎 / 不推荐

### 风险提示
- 政策风险（限购/限贷）
- 流动性风险（变现周期）
- 维护成本（物业费/维修）
- 空置风险（出租困难地区）
```

## 注意事项

- 估值仅作参考，实际成交价受多种因素影响
- 不同城市估值逻辑差异大（一线 vs 二线 vs 三线）
- 投资有风险，房产不代表稳赚