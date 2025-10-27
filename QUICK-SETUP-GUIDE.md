# 📊 价格追踪器 Google Sheets - 快速设置指南

## ⚡ 5分钟快速设置

### 第一步：创建新的 Google Sheets
1. 访问 https://sheets.google.com
2. 点击"空白"创建新表格
3. 命名为"价格追踪器"

### 第二步：导入模板数据

#### 方法A：手动导入CSV文件（推荐）

1. **创建第一个工作表 - Products**
   - 在默认的"Sheet1"中
   - 文件 → 导入 → 上传 → 选择 `template-1-products.csv`
   - 导入位置：替换当前工作表
   - 重命名工作表为"Products"

2. **创建第二个工作表 - Price Records**
   - 点击左下角"+"新建工作表
   - 文件 → 导入 → 上传 → 选择 `template-2-price-records.csv`
   - 导入位置：替换当前工作表
   - 重命名工作表为"Price Records"

3. **创建第三个工作表 - Stores**
   - 重复上述步骤，导入 `template-3-stores.csv`
   - 重命名为"Stores"

4. **创建第四个工作表 - Categories**
   - 导入 `template-4-categories.csv`
   - 重命名为"Categories"

5. **创建第五个工作表 - To-Buy List**
   - 导入 `template-5-tobuy-list.csv`
   - 重命名为"To-Buy List"

6. **创建第六个工作表 - Price Analysis**
   - 手动创建（见下方）

#### 方法B：手动输入（如果无法导入CSV）

按照 `google-sheets-template-README.md` 文档中的表头定义手动创建

---

## 🎯 Price Analysis 工作表设置

这是最重要的分析表，需要手动创建：

### 1. 创建表头（第1行）
```
A1: Product ID
B1: Product Name
C1: Current Low
D1: Best Store
E1: Historical Low
F1: Average Price
G1: Price Trend
H1: Total Records
I1: Last Updated
```

### 2. 格式化表头
- 选中第1行
- 加粗
- 背景色：`#667eea`
- 文字颜色：白色
- 对齐：水平居中

### 3. 从Products表导入产品ID
在A2单元格输入：
```
=Products!A2
```
然后向下拖动填充所有产品

### 4. 添加公式

**B2 - 产品名称：**
```
=VLOOKUP(A2, Products!A:B, 2, FALSE)
```

**C2 - 当前最低价：**
```
=IFERROR(MIN(FILTER('Price Records'!E:F, 'Price Records'!C:C=A2, 'Price Records'!B:B=MAX(FILTER('Price Records'!B:B, 'Price Records'!C:C=A2)))), "-")
```

**E2 - 历史最低价：**
```
=IFERROR(MIN(FILTER('Price Records'!E:F, 'Price Records'!C:C=A2)), "-")
```

**F2 - 平均价格：**
```
=IFERROR(AVERAGE(FILTER('Price Records'!E:F, 'Price Records'!C:C=A2)), "-")
```

**H2 - 记录数：**
```
=COUNTIF('Price Records'!C:C, A2)
```

**I2 - 最后更新：**
```
=IFERROR(TEXT(MAX(FILTER('Price Records'!B:B, 'Price Records'!C:C=A2)), "YYYY-MM-DD"), "-")
```

### 5. 复制公式到所有行
- 选中 B2:I2
- 拖动右下角的小方块向下填充所有产品行

---

## 🎨 快速格式化

### 1. 所有表格的表头格式
- 选中每个工作表的第1行
- 格式 → 加粗
- 背景色：`#667eea`
- 文字颜色：白色

### 2. 冻结表头
在每个工作表：
- 视图 → 冻结 → 1行

### 3. Price Records 条件格式

**高亮最低价：**
1. 选中 E2:F（Regular Price 和 Promo Price列）
2. 格式 → 条件格式
3. 格式规则：自定义公式
4. 公式：`=E2=MIN($E2:$F2)`
5. 格式样式：背景色 `#c6efce`（浅绿）

**高亮促销价：**
1. 选中 F列（Promo Price）
2. 格式 → 条件格式
3. 格式规则：单元格不为空
4. 格式样式：背景色 `#ffe699`（浅橙）

### 4. 调整列宽
双击列标题之间的分隔线，自动调整列宽

---

## 📱 移动端设置

### 安装 Google Sheets App
- iOS: https://apps.apple.com/app/google-sheets/id842849113
- Android: https://play.google.com/store/apps/details?id=com.google.android.apps.docs.editors.sheets

### 启用离线访问
1. 打开 Google Sheets App
2. 点击文件旁边的"..."菜单
3. 开启"可离线使用"

---

## ✅ 验证设置

设置完成后，检查以下内容：

- [ ] 所有6个工作表已创建
- [ ] Products 表有示例产品数据
- [ ] Price Records 表有示例价格记录
- [ ] Stores 表有商店列表
- [ ] Categories 表有品类定义
- [ ] To-Buy List 表有购物清单
- [ ] Price Analysis 表的公式正常运行（显示数字而非错误）
- [ ] 所有表头已格式化（紫色背景+白色文字）
- [ ] 表头行已冻结
- [ ] Price Records 的条件格式正常（最低价显示绿色背景）

---

## 🎓 下一步

设置完成后，你可以：

1. **删除示例数据**，添加你自己的产品
2. **阅读详细文档** `google-sheets-template-README.md`
3. **创建促销计算器**工作表（可选）
4. **设置数据验证**，确保输入的商店名称来自Stores表
5. **创建图表**，可视化价格趋势

---

## ❓ 常见问题

**Q: 公式显示 #REF! 错误**
A: 检查工作表名称是否正确，确保引用的工作表名称与实际名称一致

**Q: FILTER 函数不工作**
A: FILTER 是 Google Sheets 的函数，确保你使用的是 Google Sheets 而非 Excel

**Q: 如何批量添加产品？**
A: 直接在 Products 表中复制粘贴多行数据即可

**Q: 可以分享给家人使用吗？**
A: 可以！点击右上角"共享"按钮，输入邮箱地址，设置为"编辑者"权限

---

## 📞 需要帮助？

- 查看详细文档：`google-sheets-template-README.md`
- Google Sheets 帮助：https://support.google.com/docs/topic/9054603
- 函数参考：https://support.google.com/docs/table/25273

---

**预计设置时间**: 5-10分钟
**难度级别**: ⭐⭐ 初级-中级

祝你使用愉快！📊✨
