# PRD：InfunEase / Youdao Ads 账户资料与社交账号验证模块

> Scope note, 2026-07-02:
> This document is now a Payee Center submodule reference for the Collab
> platform. The platform-level source of truth is `README.md`. Personal Info,
> Social Account Verification, and Receiving Account collection should support
> creator payment readiness for confirmed campaign matches; they should not imply
> a full wallet balance or creator-initiated withdrawal product in V0.

版本：V1.0  
日期：2026-06-30  
产品范围：支付系统中的个人资料、收款账户添加、社交媒体账号验证  
适用端：Web 后台，当前截图来自移动浏览器访问桌面后台  
目标用户：创作者 / 合作方用户 / 广告合作参与者

## 1. 产品背景

InfunEase / Youdao Ads 是面向创作者或合作方的广告合作与支付管理系统。用户需要完成个人资料、社交媒体账号验证和收款账户绑定后，才能接收合作邀请、参与项目并进行提现。

当前截图覆盖三个核心业务域：

- 个人资料维护
- 社交媒体账号验证
- 收款账户添加与提现前置配置

## 2. 产品目标

### 2.1 业务目标

- 确认用户身份和联系方式。
- 验证用户拥有指定社交媒体账号。
- 支持合作邀请、项目参与、数据评估和资金提现。
- 降低账号冒用、虚假账号、错误打款等风险。

### 2.2 用户目标

- 快速补全个人信息。
- 绑定并验证自己的社交平台账号。
- 添加可用于收款的银行账户。
- 顺利参与合作项目并完成提现。

## 3. 角色与使用场景

### 3.1 用户角色

| 角色 | 说明 |
| --- | --- |
| 创作者用户 | 绑定 YouTube、TikTok、Instagram 等平台账号，接收合作邀请 |
| 合作方用户 | 参与官方或自由合作项目 |
| 平台运营/审核人员 | 审核截图验证、处理异常绑定、管理用户资质 |
| 财务/支付系统 | 校验收款账户，处理提现 |

### 3.2 核心场景

1. 用户首次进入系统，补全个人资料。
2. 用户进入 Account Verification 页面，验证社交媒体账号。
3. 用户通过 OAuth 授权 YouTube 账号。
4. 用户完成平台账号验证后，参与合作项目。
5. 用户进入钱包或提现页面，添加收款账户。
6. 用户通过三步流程提交收款账户信息。

## 4. 信息架构

系统当前可识别的导航结构如下：

- Offer Browser
- Official Business Collaboration
  - My Projects
- Freelancing Business Collaborations
  - Collaboration Invitation
  - My Projects
- My Wallet
- Personal Center
  - Account Verification
  - Personal Info
  - Notifications

## 5. 功能需求

### 5.1 顶部通用导航

#### 功能说明

所有后台页面顶部展示统一导航能力。

#### 页面元素

- 品牌 Logo：InfunEase / Youdao Ads
- 帮助入口：问号 icon
- 通知入口：铃铛 icon
- 语言切换：地球 icon + 当前语言，例如 English
- 用户头像 / 用户菜单：例如 `zm`

#### 交互规则

- 点击帮助 icon，进入帮助中心或打开帮助弹窗。
- 点击通知 icon，进入通知中心或展示通知浮层。
- 点击语言切换，展示语言列表。
- 点击头像，展示账户菜单，例如个人资料、账号安全、退出登录。

### 5.2 Personal Info 个人信息模块

#### 功能说明

用户维护基础身份、联系方式和地址信息。该信息可能用于 KYC、合作资质、账单、风控和提现校验。

#### 页面字段

| 字段 | 类型 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| Name | 输入框 | 建议必填 | 用户姓名 |
| Phone Number | 国家区号 + 手机号 | 建议必填 | 用户联系电话 |
| Country/Region | 下拉选择 | 建议必填 | 用户所在国家/地区 |
| Province | 输入框 | 根据国家动态判断 | 省份 |
| City | 输入框 | 根据国家动态判断 | 城市 |
| State | 输入框 | 根据国家动态判断 | 州/行政区 |
| Address | 多行文本框 | 建议必填 | 详细地址 |

#### 交互规则

1. 用户进入 Personal Info 页面后，可编辑表单字段。
2. 手机号字段由国家区号选择和手机号输入组成。
3. Country/Region 选择后，应联动地址字段规则。
4. Address 旁的信息 icon 可展示填写说明。
5. 页面底部应提供保存按钮，当前截图未展示，推测在页面下方。

#### 校验规则

- Name 不允许为空。
- 手机号需校验区号和号码格式。
- Country/Region 不允许为空。
- Address 不允许为空。
- Province / State / City 应根据国家动态决定必填项。

#### 产品优化建议

- 移动端将 Phone Number 拆为上下布局。
- State、Province 字段根据国家动态显示，避免语义重复。
- 保存按钮建议固定在底部，提高可发现性。

### 5.3 Account Verification 社交账号验证模块

#### 功能说明

用户需要验证社交媒体账号，才能接收合作邀请并参与合作项目。

#### 支持平台

| 平台 | 验证入口 |
| --- | --- |
| YouTube | Verify |
| TikTok | Verify |
| Twitch | Verify |
| Twitter / X | Verify |
| Facebook | Verify |
| Instagram | Verify |
| Other platforms | Verify |

#### 页面说明

页面顶部展示两条提示：

1. 社交媒体账号验证是接收邀请和参与合作项目的必要条件。
2. 用户需要验证参与活动所关联的平台账号，才能接收 Collaboration Invitations 和 My Collaboration Projects 的更新。

#### 平台行结构

每个平台一行，包含：

- 平台 icon
- 平台名称
- 已验证账号数量，例如 `1 account(s) verified`
- 操作按钮：`Verify`

#### 状态规则

| 状态 | 页面表现 |
| --- | --- |
| 未验证 | 只显示 Verify 按钮 |
| 已验证 | 显示 `n account(s) verified`，同时保留 Verify 按钮 |
| 验证成功 | 顶部 Toast：`Social media adding successful.` |
| 验证失败 | 展示失败 Toast 或错误提示 |
| 待审核 | 截图验证提交后显示 Pending 状态 |

#### 关键逻辑

- 同一平台支持绑定多个账号。
- 已验证平台仍保留 Verify 按钮，用于继续添加账号。
- 每个平台验证状态独立管理。
- 账号验证结果会影响合作邀请和项目参与权限。

### 5.4 社交账号验证方式弹窗

#### 功能说明

用户点击某个平台的 Verify 后，打开验证方式选择弹窗。

#### 弹窗标题

`Social Media Account Verification (Choose ONLY ONE method)`

表示用户只能选择一种验证方式。

#### Method 1：Social OAuth

页面元素：

- 标签：Method 1
- 标题：Social OAuth
- 说明：Click to complete social media account verification
- 隐私说明：系统只采集 profile name 和 URL link 用于验证
- 按钮：Redirect to verify
- 按钮展示对应平台 icon，例如 YouTube

交互流程：

1. 用户点击 Redirect to verify。
2. 系统跳转至第三方平台授权页面。
3. 用户确认授权。
4. 第三方平台回调系统。
5. 系统获取账号信息。
6. 系统完成账号绑定。
7. 页面刷新验证数量。
8. 展示成功 Toast。

#### Method 2：Screenshot Verification

页面元素：

- 标签：Method 2
- 标题：Screenshot Verification
- 说明：通过提交频道截图和频道链接完成验证。
- 要求：截图需清晰展示创作者头像和频道名称。
- 预期字段：频道链接、截图上传、提交按钮。

交互流程：

1. 用户填写频道链接。
2. 用户上传账号所有权证明截图。
3. 用户提交验证。
4. 系统进入待审核状态。
5. 审核通过后，平台账号数 +1。
6. 审核失败后，展示失败原因。

#### 异常处理

| 异常 | 处理方式 |
| --- | --- |
| 用户取消 OAuth | 返回系统，提示授权已取消 |
| 授权失败 | 提示失败原因，允许重试 |
| 权限不足 | 提示需重新授权必要权限 |
| 账号已绑定 | 提示该账号已被绑定 |
| 截图不清晰 | 审核失败并提示重新上传 |
| 链接格式错误 | 阻止提交并提示修正 |

### 5.5 Google / YouTube OAuth 授权

#### 功能说明

YouTube 验证通过 Google OAuth 完成。用户在 Google 官方页面授权 Youdao Ads 访问相关权限。

#### 授权信息

- 授权应用：Youdao Ads
- 授权账号：用户 Google 账号
- 授权域名：accounts.google.com

#### 权限范围

截图中可见权限包括：

1. 查看用户的 YouTube 账号。
2. 查看有关用户 YouTube 内容的 YouTube Analytics 报告。

#### 产品风险

当前系统弹窗说明“只收集 profile name 和 URL link”，但 Google 授权页显示还会访问 YouTube Analytics 报告。这里存在明显的信任与合规风险。

#### PRD 要求

- 系统内必须明确说明实际申请的权限范围。
- 如果 YouTube Analytics 是必需权限，需要说明使用目的。
- 如果 Analytics 不是必需权限，应拆分为可选授权。
- OAuth 授权需使用 state 参数防止 CSRF。
- 授权失败后应允许用户改用 Screenshot Verification。

### 5.6 My Wallet / 收款账户添加模块

#### 功能说明

用户在钱包或提现场景中添加新的收款账户。当前流程为三步式表单。

#### 弹窗标题

`Add new account`

#### 步骤结构

| 步骤 | 名称 | 说明 |
| --- | --- | --- |
| 1 | Beneficiary Info | 受益人基础信息 |
| 2 | Account Info | 银行账户信息 |
| 3 | Confirmation | 信息确认 |

#### Step 1：Beneficiary Info

| 字段 | 类型 | 是否必填 | 说明 |
| --- | --- | --- | --- |
| Beneficiary name | 输入框 | 是 | 收款人姓名 |
| Country | 下拉选择 | 是 | 收款人国家 |
| Transfer Method | 单选/选择框 | 是 | 当前为 SWIFT |
| Account Currency | 下拉选择 | 是 | 收款账户币种 |
| Date of Birth | 日期选择器 | 否 | 出生日期 |

Country 字段采用二级选择结构。一级地区包括：

- Africa
- Australia
- Commonwealth of Independent States
- East Asia
- Europe
- North America

交互规则：

1. 点击 Country，展开地区分组菜单。
2. 点击地区，展示该地区下的国家。
3. 选择国家后，填入 Country 字段。
4. Country 可能联动 Account Currency。
5. 点击 Account Currency，选择币种。
6. 点击 Date of Birth，打开日期选择器。
7. 点击 Next 前校验必填字段。

校验规则：

- Beneficiary name 不可为空。
- Country 不可为空。
- Transfer Method 不可为空。
- Account Currency 不可为空。
- Date of Birth 为选填。

#### Step 2：Account Info

截图未展示，但根据流程推测应包含：

| 字段 | 说明 |
| --- | --- |
| Bank Name | 银行名称 |
| Bank Country/Region | 银行所在国家 |
| Account Number / IBAN | 银行账号 |
| SWIFT/BIC Code | SWIFT 转账代码 |
| Bank Address | 银行地址 |
| Beneficiary Address | 收款人地址 |
| Routing Number | 特定国家需要 |
| Sort Code | 英国等地区需要 |
| Branch Code | 部分国家需要 |

#### Step 3：Confirmation

截图未展示，但应包含：

- 受益人信息确认。
- 银行账户信息确认。
- 币种确认。
- 转账方式确认。
- 风险提示。
- 提交按钮。
- 返回修改入口。

#### 弹窗通用交互

- 右上角 X：关闭弹窗。
- Back：返回上一步或取消。
- Next：进入下一步。
- 背景遮罩：阻断主页面操作。
- 表单内容过长时弹窗内部滚动。

### 5.7 钱包状态模块

从背景页面可见资金状态 Tab：

- Unpaid
- Received
- Withdrawn

推测功能：

| 状态 | 说明 |
| --- | --- |
| Unpaid | 待支付 / 待结算金额 |
| Received | 已到账金额 |
| Withdrawn | 已提现金额 |

该模块应与提现、收款账户绑定、余额展示相关。

## 6. 核心业务流程

### 6.1 社交账号 OAuth 验证流程

1. 用户进入 Account Verification。
2. 用户点击 YouTube 的 Verify。
3. 系统打开验证方式弹窗。
4. 用户选择 Social OAuth。
5. 系统跳转 Google OAuth。
6. 用户选择授权权限。
7. Google 回调系统。
8. 系统读取 YouTube 账号信息。
9. 系统保存绑定记录。
10. 页面展示 `1 account(s) verified`。
11. 顶部展示成功 Toast。

### 6.2 截图验证流程

1. 用户点击平台 Verify。
2. 用户选择 Screenshot Verification。
3. 用户填写频道链接。
4. 用户上传账号截图。
5. 用户提交审核。
6. 系统展示 Pending。
7. 审核通过后展示已验证数量。
8. 审核失败后展示失败原因。

### 6.3 添加收款账户流程

1. 用户进入 My Wallet。
2. 用户点击 Add new account。
3. 系统打开三步弹窗。
4. 用户填写 Beneficiary Info。
5. 用户填写 Account Info。
6. 用户确认信息。
7. 用户提交。
8. 系统保存收款账户。
9. 用户可在提现时选择该账户。

## 7. 非功能需求

### 7.1 安全要求

- OAuth 必须使用 state 参数防 CSRF。
- 敏感银行账户信息需加密存储。
- 授权 token 需安全存储，支持失效刷新。
- 用户关闭或离开页面时避免泄露表单数据。
- 银行账户提交需有风控校验。

### 7.2 合规要求

- 明确说明 OAuth 权限用途。
- 隐私说明必须与实际申请权限一致。
- 提供隐私政策和服务条款入口。
- 截图验证材料需说明用途和保存周期。
- 用户应可解绑已验证账号。

### 7.3 兼容性要求

- 当前移动端存在明显横向溢出，需适配移动浏览器。
- 弹窗宽度应响应式处理。
- 表单字段在移动端应纵向排列。
- 长文案应换行或折叠展示。
- 重要按钮不得超出可视区域。

## 8. 状态与提示

| 场景 | 提示方式 |
| --- | --- |
| 社交账号添加成功 | Toast：Social media adding successful. |
| OAuth 授权取消 | Toast 或页面提示 |
| OAuth 授权失败 | 错误提示 + 重试入口 |
| 截图提交成功 | Pending 状态 |
| 截图审核失败 | 展示失败原因 |
| 收款账户提交成功 | 成功提示 |
| 表单校验失败 | 字段下方错误提示 |
| 关闭未保存弹窗 | 二次确认 |

## 9. 当前体验问题与优化建议

### 9.1 高优先级

1. 移动端适配不足  
   多个页面横向溢出，弹窗超出屏幕，按钮被截断。建议提供移动端响应式布局。

2. OAuth 权限说明不一致  
   系统说明只采集 profile name 和 URL，但 Google 实际展示 YouTube Analytics 权限。建议修正文案，明确权限用途。

3. 验证弹窗成功后仍停留  
   成功 Toast 出现后弹窗仍存在。建议成功后自动关闭弹窗，或切换为成功状态。

### 9.2 中优先级

1. 国家选择缺少搜索  
   只能按地区层级选择。建议增加国家搜索、常用国家置顶。

2. 地址字段语义混乱  
   Province、State 同时出现。建议根据国家动态展示字段。

3. 截图验证内容不完整  
   移动端看不到完整上传和提交区域。建议优化弹窗滚动和底部操作按钮固定。

## 10. 数据指标

| 指标 | 目的 |
| --- | --- |
| Personal Info 完成率 | 衡量资料补全效率 |
| 社交账号验证点击率 | 衡量用户验证意愿 |
| OAuth 授权成功率 | 衡量授权流程顺畅度 |
| OAuth 取消率 | 判断权限说明是否造成流失 |
| 截图验证提交率 | 衡量备用验证使用情况 |
| 截图审核通过率 | 衡量材料质量 |
| 收款账户添加成功率 | 衡量提现前置流程效率 |
| 表单校验失败率 | 发现字段设计问题 |
| 移动端横向滚动率 | 衡量响应式问题严重程度 |

## 11. MVP 范围

### 11.1 必须包含

- Personal Info 编辑与保存。
- Account Verification 平台列表。
- YouTube OAuth 验证。
- 截图验证。
- 验证成功状态展示。
- 多账号数量展示。
- Add new account 三步流程。
- SWIFT 收款账户添加。
- 表单校验和错误提示。

### 11.2 可后续迭代

- 国家搜索。
- 已验证账号管理 / 解绑。
- OAuth 权限分级授权。
- 移动端独立布局。
- 审核后台。
- 验证历史记录。
- 银行账户风险校验增强。
- 多语言文案精细化。

## 12. 结论

该系统的核心闭环是：

用户资料补全 -> 社交账号验证 -> 获得合作参与资格 -> 添加收款账户 -> 完成提现。

当前功能框架已经具备基础业务能力，但需要重点补强三点：

1. 移动端响应式体验。
2. OAuth 权限透明度。
3. 验证与收款流程的状态管理和异常提示。

这些问题会直接影响用户信任、合作转化率和提现成功率，应作为下一阶段优先优化项。
