# 维度智能分组功能需求文档（规则引擎版本）

## 1. 产品背景

### 1.1 现状描述
BI数据平台目前提供自定义维度分组功能，用户可以将维度值（如渠道、地区等）进行手动分组，便于汇总统计收入、新增等关键指标。

### 1.2 核心痛点
- **效率瓶颈**：每当出现新的维度值，用户必须手动将其添加到对应分组
- **频繁更新**：维度值（特别是渠道）更新频繁，人工维护成本高
- **规模挑战**：维度值数量庞大，手动分组难以应对

### 1.3 典型场景
投放团队面临的实际问题：
- 渠道名称如"今日头条(效果)"、"头条直播(效果)"、"头条原生(效果)"需要归为一组
- 希望设置类似`头条*(效果)`的规则，自动捕获所有符合模式的渠道
- 当出现新的头条系渠道时，能自动归类，无需人工干预

## 2. 产品目标

### 2.1 核心目标
通过**规则引擎**实现维度值的自动分组，大幅提升分组效率，减少人工维护成本。

### 2.2 预期效果
- 新增维度值自动归类，减少90%以上的手动操作
- 支持灵活的规则配置，满足不同业务场景需求
- 保持现有手动分组能力，实现平滑过渡
- 规则逻辑透明可控，便于调试和维护

## 3. 功能需求

### 3.1 智能分组规则引擎

#### 3.1.1 通配符匹配规则
**需求描述**：支持通配符模式匹配，实现批量自动分组

**功能规格**：
- 支持`*`通配符：匹配任意长度的任意字符（包括0个字符）
- 支持`%`模糊匹配：等同于SQL中的LIKE操作符
- 支持`?`单字符匹配：匹配单个任意字符

**规则示例**：
```
头条*           → 匹配以"头条"开头的所有字符串
*效果*          → 匹配包含"效果"的所有字符串  
头条*(效果)     → 匹配以"头条"开头，以"(效果)"结尾的字符串
%青春服%        → 匹配包含"青春服"的所有字符串
抖音*直播*      → 匹配包含"抖音"和"直播"的字符串，且"抖音"在前
```

**使用场景**：
```
规则配置：头条*(效果) → 头条效果投放组
自动匹配：
- 今日头条(效果) ✓
- 头条直播(效果) ✓  
- 头条原生(效果) ✓
- 头条品牌(效果) ✓
```

#### 3.1.2 多关键词组合规则
**需求描述**：支持多个关键词的逻辑组合匹配

**功能规格**：
- **AND逻辑**：同时包含多个关键词
- **OR逻辑**：包含任一关键词即可
- **NOT逻辑**：排除特定关键词

**配置示例**：
```
包含：头条 AND 效果     → 必须同时包含两个关键词
包含：抖音 OR 快手      → 包含任一关键词即可
包含：直播 NOT 测试     → 包含"直播"但不包含"测试"
包含：头条 AND 效果 NOT 测试 → 复合条件组合
```

#### 3.1.3 分组优先级机制
**需求描述**：当一个维度值匹配多个分组规则时，按分组优先级顺序处理

**设计原则**：
- 每个分组对应唯一一条匹配规则
- 分组支持数字编号排序（1号分组、2号分组...）
- 按分组序号从小到大优先匹配
- 一旦匹配成功，停止后续分组规则检查
- 支持拖拽调整分组优先级

**处理逻辑**：
```
分组1（优先级1）：头条效果组 - 规则：头条*(效果)
分组2（优先级2）：头条全渠道组 - 规则：头条*

对于"今日头条(效果)"：
- 匹配分组1规则 ✓ → 归入"头条效果组"
- 不再检查分组2规则
```

### 3.2 混合分组策略

#### 3.2.1 分组优先级层次
1. **手动分组**：用户已手动分配的维度值，保持不变，优先级最高
2. **规则分组**：未分组的维度值，按规则自动匹配
3. **其他归类**：无法匹配任何规则的，可选择归入"其他"分组
4. **冲突不处理**：匹配多个同优先级规则产生冲突的，保持未分组状态

#### 3.2.2 冲突处理机制
**冲突场景**：某个维度值同时匹配多个分组的规则

**处理方案**：
- 如果分组有不同优先级，按高优先级（小序号）分组
- 如果分组优先级相同，该维度值保持未分组状态，不执行自动分组
- 用户可以通过手动分组或调整分组优先级来解决冲突
- 系统提供冲突检测功能，主动提醒用户存在冲突的分组

**冲突示例**：
```
分组A（优先级2）：直播渠道组 - 规则：*直播*
分组B（优先级2）：头条渠道组 - 规则：*头条*

对于"头条直播(效果)"：
- 同时匹配分组A和分组B的规则
- 由于优先级相同，保持未分组状态
- 系统在冲突检测中提醒用户
- 用户可选择：①手动分组 ②调整分组优先级 ③修改分组规则
```

### 3.3 规则管理界面

#### 3.3.1 分组规则配置功能

**核心功能需求**：
- 支持创建、编辑、删除分组及其对应的匹配规则
- 支持分组优先级调整，通过拖拽或数字设置
- 提供分组规则的启用/禁用功能
- 支持分组和规则的批量操作（批量启用/禁用、批量删除等）

**配置字段需求**：
- **分组名称**：分组的名称标识（必填，支持重命名）
- **分组描述**：可选的分组说明和用途描述
- **匹配模式**：选择通配符/关键词组合/精确匹配
- **匹配规则**：具体的匹配字符串或组合条件
- **优先级**：数字越小优先级越高（系统自动分配，用户可调整）
- **启用状态**：支持分组规则的启用/禁用切换

**编辑体验需求**：
- 每个分组有且仅有一条匹配规则
- 修改规则时提供实时预览功能，显示匹配效果
- 支持规则模板选择，快速配置常用模式
- 提供语法检查和错误提示
- 支持规则复制功能，便于创建相似分组

#### 3.3.2 规则测试与验证

**测试功能目标**：
为用户提供整体分组测试页面，让用户能够验证当前所有分组规则的配置是否合理，包括各分组的匹配规则和优先级设置。

**测试数据来源**：
- **选择现有数据**：从当前维度下已有的维度值中选择作为测试数据
- **手动输入数据**：用户手动输入待测试的维度值样例
- **批量导入数据**：支持复制粘贴多个维度值进行批量测试

**测试功能需求**：
- **整体分组测试**：将测试数据在所有分组规则下进行匹配，展示最终分组结果
- **规则语法检查**：检查各分组规则语法是否正确，提示错误信息
- **优先级验证**：验证分组优先级设置是否符合用户预期
- **冲突识别**：识别同时匹配多个同优先级分组的数据

**测试结果展示需求**：
```
分组测试结果：
================
测试数据：5条

📋 分组结果：
┌─ 头条效果渠道（优先级1）
│  ├─ 今日头条(效果)
│  └─ 头条直播(效果)
│
┌─ 抖音渠道（优先级2）  
│  └─ 抖音直播(效果)
│
┌─ 未分组
│  ├─ 头条品牌投放 [无匹配规则]
│  └─ 快手效果 [无匹配规则]

⚠️ 需要注意：
- 无冲突数据
- 2条数据未分组，可考虑添加对应分组规则
```

**测试页面功能需求**：
- 支持在测试页面直接修改分组匹配规则
- 修改规则后用户可重新执行测试，查看结果变化
- 提供测试数据的保存和重用功能
- 支持导出测试结果，便于分析和讨论
- 测试过程不影响实际的分组数据
- 提供"应用到生产"功能，测试满意后可直接应用分组规则

#### 3.3.3 分组规则导入导出
**导出功能**：
- 支持将分组规则配置导出为JSON/Excel格式
- 方便在不同环境间迁移分组配置
- 支持分组规则备份和版本管理

**导入功能**：
- 支持从文件批量导入分组规则
- 导入前进行分组规则验证和冲突检查
- 支持增量导入和覆盖导入两种模式

### 3.4 分组结果管理

#### 3.4.1 分组概览
- 显示各分组的维度值数量和占比
- 特别标注"未分组"和"其他"的数据量
- 提供冲突规则检测，显示存在冲突的维度值数量
- 提供分组结果的导出功能（Excel/CSV格式）
- 支持分组数据的可视化展示（饼图/柱状图）

#### 3.4.2 手动调整功能
**个别调整**：
- 支持从自动分组结果中调整个别维度值
- 调整后的手动分组优先级最高，不被规则覆盖
- 记录手动调整的历史，便于追溯

**批量调整**：
- 支持多选维度值，批量调整到指定分组
- 提供"批量移动到其他组"功能
- 支持批量处理冲突的维度值（统一分组或调整规则）
- 支持批量添加到排除列表

## 4. 用户体验设计

### 4.1 界面设计原则
- **渐进式复杂度**：从简单配置开始，逐步支持复杂规则
- **所见即所得**：规则配置即时预览分组效果
- **容错设计**：提供撤销、重置等容错机制
- **引导式操作**：为新用户提供向导式配置流程

### 4.2 交互设计要点
**规则配置向导**：
- 第一步：选择匹配模式（通配符/关键词/精确匹配）
- 第二步：输入匹配条件，实时显示匹配预览
- 第三步：选择目标分组，设置优先级
- 第四步：确认规则并保存

### 4.3 帮助与文档
**内置帮助**：
- 通配符语法的详细说明和示例
- 常见业务场景的规则模板


## 5. 附录

### 5.1 通配符语法参考
```
*     匹配任意长度字符    头条*     → 头条直播、头条原生
%     SQL风格模糊匹配    %效果%    → 某某效果、效果投放  
?     匹配单个字符        头条?     → 头条A、头条1
^     以...开头         ^头条     → 头条开头的字符串
$     以...结尾         效果$     → 以效果结尾的字符串
```

### 5.2 常见分组规则模板
```
# 渠道分组模板
分组：头条效果类渠道   规则：头条*(效果)
分组：抖音全渠道       规则：*抖音*
分组：信息流投放       规则：%信息流%
分组：直播渠道(非测试) 规则：*直播* NOT *测试*

# 地区分组模板  
分组：省级地区         规则：*省
分组：市级地区         规则：*市
分组：一线城市         规则：北京*|上海*|广州*

# 产品分组模板
分组：青春服装类       规则：*青春服*
分组：运动用品类       规则：*运动*
分组：数码产品类       规则：%数码%