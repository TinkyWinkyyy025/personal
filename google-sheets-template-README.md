# 价格追踪器 Google Sheets 模板

## 📋 模板结构

这个Google Sheets模板包含以下工作表（Sheets）：

### 1. **产品列表 (Products)**
存储所有产品的基本信息

### 2. **价格记录 (Price Records)**
记录每个产品在不同商店、不同日期的价格

### 3. **商店列表 (Stores)**
管理所有商店信息

### 4. **品类管理 (Categories)**
管理产品品类和子品类

### 5. **购物清单 (To-Buy List)**
待购商品清单

### 6. **价格分析 (Price Analysis)**
自动计算价格统计数据（最低价、平均价、价格趋势等）

---

## 🚀 使用步骤

### 步骤 1: 创建新的 Google Sheets
1. 访问 [Google Sheets](https://sheets.google.com)
2. 点击 "空白" 创建新表格
3. 命名为 "价格追踪器"

### 步骤 2: 创建工作表
创建以下6个工作表（Sheet）：
- Products
- Price Records
- Stores
- Categories
- To-Buy List
- Price Analysis

### 步骤 3: 设置表头
根据下面的模板设置每个工作表的表头和格式

---

## 📊 各工作表详细说明

### 1️⃣ Products (产品列表)

**列定义：**
| 列 | 名称 | 说明 | 示例 |
|---|---|---|---|
| A | Product ID | 产品唯一标识 | `hydrating-88` |
| B | Product Name | 产品名称 | `CeraVe Hydrating Cleanser 88ml` |
| C | Brand | 品牌 | `CeraVe` |
| D | Category | 品类 | `护肤` |
| E | Subcategory | 子品类 | `Cleanser` |
| F | Enabled Stores | 启用的商店（逗号分割） | `Guardian,Watsons,Shopee` |
| G | Notes | 备注 | - |

**设置建议：**
- 第1行：表头，加粗，背景色 `#667eea`，文字白色
- 冻结第1行：视图 → 冻结 → 1行

---

### 2️⃣ Price Records (价格记录)

**列定义：**
| 列 | 名称 | 说明 | 示例 |
|---|---|---|---|
| A | Record ID | 记录ID（自动） | `1` |
| B | Date | 日期 | `2024-01-15` |
| C | Product ID | 产品ID（关联Products） | `hydrating-88` |
| D | Store | 商店名称 | `Guardian` |
| E | Regular Price | 原价 (RM) | `25.90` |
| F | Promo Price | 促销价 (RM) | `19.43` |
| G | Promo Type | 促销类型 | `2nd Item -50%` |
| H | Remark | 备注 | `Member exclusive` |

**设置建议：**
- 第1行：表头，加粗，背景色 `#667eea`，文字白色
- 日期列格式：格式 → 数字 → 日期
- 价格列格式：格式 → 数字 → 货币
- 数据验证：Store列应该只允许选择Stores表中的商店

**关键公式：**
在 I 列添加"有效价格"（Effective Price）：
```
=IF(ISBLANK(F2), E2, F2)
```
这会自动选择促销价，如果没有促销价则使用原价

---

### 3️⃣ Stores (商店列表)

**列定义：**
| 列 | 名称 | 说明 | 示例 |
|---|---|---|---|
| A | Store Name | 商店名称 | `Guardian` |
| B | Type | 类型 | `实体店` / `线上` |
| C | Website | 网址（可选） | `www.guardian.com.my` |
| D | Notes | 备注 | - |

**预设商店：**
- Guardian
- Watsons
- Caring
- Shopee
- Lazada
- Sephora

---

### 4️⃣ Categories (品类管理)

**列定义：**
| 列 | 名称 | 说明 | 示例 |
|---|---|---|---|
| A | Category ID | 品类ID | `skincare` |
| B | Category Name | 品类名称 | `护肤` |
| C | Subcategories | 子品类（逗号分割） | `Cleanser,Toner,Serum` |
| D | Product Count | 产品数量 | `15` |

**预设品类：**
- skincare (护肤): Cleanser, Toner, Serum, Moisturizer, Sunscreen, Eye Cream, Mask
- makeup (美妆): Foundation, Concealer, Powder, Blush, Eyeshadow, Lipstick, Mascara
- clothing (衣服): T-Shirt, Pants, Dress, Jacket, Shoes
- accessories (配饰): Bag, Watch, Jewelry, Hat, Belt

**公式示例（D列 - 产品数量）：**
```
=COUNTIF(Products!D:D, A2)
```

---

### 5️⃣ To-Buy List (购物清单)

**列定义：**
| 列 | 名称 | 说明 | 示例 |
|---|---|---|---|
| A | Item Name | 商品名称 | `iPhone 15 Pro` |
| B | Preferred Store | 期望商店 | `Shopee` |
| C | Target Price | 目标价格 (RM) | `4500.00` |
| D | Current Best | 当前最低价 | `4799.00` |
| E | Price Gap | 价格差距 | `299.00` |
| F | Notes | 备注 | `等12.12促销` |
| G | Date Added | 添加日期 | `2024-01-15` |
| H | Status | 状态 | `待购` / `已购` |

**公式示例：**

**E列 - 价格差距：**
```
=IF(AND(NOT(ISBLANK(C2)), NOT(ISBLANK(D2))), D2-C2, "")
```

**D列 - 当前最低价（需要从Price Records查询）：**
```
=IFERROR(MIN(FILTER('Price Records'!I:I, 'Price Records'!C:C=VLOOKUP(A2, Products!B:A, 1, FALSE))), "暂无数据")
```

---

### 6️⃣ Price Analysis (价格分析)

**列定义：**
| 列 | 名称 | 说明 | 计算公式 |
|---|---|---|---|
| A | Product ID | 产品ID | - |
| B | Product Name | 产品名称 | `=VLOOKUP(A2, Products!A:B, 2, FALSE)` |
| C | Current Low | 当前最低价 | 见下方 |
| D | Best Store | 最佳商店 | 见下方 |
| E | Historical Low | 历史最低价 | 见下方 |
| F | Average Price | 平均价格 | 见下方 |
| G | Price Trend | 价格趋势 | `▼` / `▲` / `—` |
| H | Total Records | 记录数 | 见下方 |
| I | Last Updated | 最后更新 | 见下方 |

**关键公式：**

**C列 - 当前最低价：**
```
=IFERROR(MIN(FILTER('Price Records'!I:I, 'Price Records'!C:C=A2, 'Price Records'!B:B=MAX(FILTER('Price Records'!B:B, 'Price Records'!C:C=A2)))), "-")
```

**D列 - 最佳商店：**
```
=IFERROR(INDEX('Price Records'!D:D, MATCH(1, ('Price Records'!C:C=A2)*('Price Records'!I:I=C2), 0)), "-")
```

**E列 - 历史最低价：**
```
=IFERROR(MIN(FILTER('Price Records'!I:I, 'Price Records'!C:C=A2)), "-")
```

**F列 - 平均价格：**
```
=IFERROR(AVERAGE(FILTER('Price Records'!I:I, 'Price Records'!C:C=A2)), "-")
```

**H列 - 记录数：**
```
=COUNTIF('Price Records'!C:C, A2)
```

**I列 - 最后更新：**
```
=IFERROR(MAX(FILTER('Price Records'!B:B, 'Price Records'!C:C=A2)), "-")
```

---

## 🎨 格式化建议

### 配色方案
- **主色调**: `#667eea` (紫色)
- **成功/最低价**: `#c6efce` (浅绿)
- **历史最低**: `#ffd700` (金色)
- **促销价**: `#ffe699` (浅橙)
- **警告**: `#fff3e0` (浅橙黄)

### 条件格式设置

#### Price Records 表格：
1. **最低价高亮**：
   - 范围：E:F列
   - 条件：值 = 当前行最小值
   - 格式：背景色 `#c6efce`

2. **促销价高亮**：
   - 范围：F列
   - 条件：单元格不为空
   - 格式：背景色 `#ffe699`

#### Price Analysis 表格：
1. **价格趋势颜色**：
   - 下降趋势 ▼：文字颜色 `#388e3c` (绿色)
   - 上升趋势 ▲：文字颜色 `#d32f2f` (红色)

---

## 🧮 促销计算器（额外工作表）

可以创建一个"Promo Calculator"工作表来快速计算促销单价：

| 促销类型 | 输入原价 | 计算公式 | 结果 |
|---|---|---|---|
| 第二件半价 | 25.90 | `=B2*1.5/2` | 19.43 |
| 第二件-30% | 25.90 | `=B3*1.7/2` | 22.02 |
| 买一送一 | 25.90 | `=B4/2` | 12.95 |
| 买二送一 | 25.90 | `=B5*2/3` | 17.27 |

---

## 📱 移动端使用建议

1. **Google Sheets App**：下载手机应用以便随时记录价格
2. **快速输入**：使用手机相机扫描价格标签，然后手动输入
3. **离线模式**：启用离线访问，无网络时也能使用

---

## 🔒 数据保护

1. **定期备份**：文件 → 创建副本
2. **版本历史**：文件 → 版本记录 → 查看版本记录
3. **共享设置**：建议设为"仅限查看者"或"仅限评论者"

---

## 💡 进阶技巧

### 1. 使用 Google Forms 快速录入
创建一个 Google Form 链接到这个 Sheet，方便手机快速录入价格

### 2. 数据透视表分析
插入 → 数据透视表，可以快速生成各种分析报告

### 3. 图表可视化
插入 → 图表，创建价格趋势图

### 4. 自动提醒
使用 Google Apps Script 设置价格低于目标时的邮件提醒

---

## 🆘 常见问题

**Q: 如何导入现有的HTML应用数据？**
A: 从HTML应用导出JSON，然后手动复制粘贴到相应的工作表中

**Q: 公式显示错误怎么办？**
A: 检查引用的工作表名称是否正确，确保所有依赖的列都有数据

**Q: 如何添加更多商店？**
A: 在Stores工作表添加新行，然后在Price Records的数据验证中更新范围

---

## 📞 支持

如有问题，请参考：
- [Google Sheets 帮助中心](https://support.google.com/docs/topic/9054603)
- [Google Sheets 函数列表](https://support.google.com/docs/table/25273)

---

**版本**: 1.0
**最后更新**: 2024-01-15
**兼容性**: Google Sheets (Web, iOS, Android)
