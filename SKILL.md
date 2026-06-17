---
name: Saudi-GAC-Risk-Screening
description: "Bulk-screen Keeta / Keemart letters, emails, and contracts — in Chinese, English, or Arabic — for language touching the GAC's competition-law investigation directions, and output a structured summary pinpointing the contract/document number and the exact quoted text. Covers predatory pricing, misleading pricing, cross-subsidization, horizontal/MFN agreements, self-preferencing, exclusive supply, resale price maintenance, territory/customer restrictions, reward-penalty tying, data sharing, ancillary restraints (no-poach etc.), and promotional/joint-marketing terms (flagged dual-use: risk vs. favorable defense evidence). Use whenever the user wants to screen one or many documents for GAC risk, points to a batch of contracts/emails/letters, or asks to check text against the GAC investigation areas. Trilingual keyword bank — a hit in any of ZH/EN/AR is logged. Identification and evidence-gathering only; never recommends altering, deleting, or concealing documents. Output is privileged attorney work product."
license: Proprietary
---

# 沙特竞争法风险文本筛查器

**用途**:批量评审信函(خطابات / letters)、邮件(بريد إلكتروني / emails)、合同(عقود / contracts)原文,识别是否涉及 GAC 拟立案的调查方向,逐条定位到**合同编号 + 具体引文**,输出结构化筛查清单。
**适用主体**:Keeta Technologies Arabia / Keemart 一方文件。
**语言**:合同/函件可能以**中文、英文、阿拉伯文**任一种签署或往来——三语敏感词须**对等覆盖**,任一语言命中即记录。
**法律框架**:沙特竞争法(نظام المنافسة, Royal Decree M/75)第 5 条(الاتفاقات المخلة بالمنافسة / 限制竞争协议)、第 6 条(إساءة استخدام الوضع المهيمن / 滥用市场支配地位)、第 19 条(الغرامات / 罚款,GAC 口径按总销售额 10% / 上一案量级)。
**定性**:本输出为律师工作成果(privileged work product / منتج عمل محامٍ)。仅作**风险识别与证据归集**,不得据此建议修改、删改或销毁任何文件;对存疑文本只标记"提交律师复核",由 counsel 决定。

---

## 〇、输入处理(读取源文件)

- **已是纯文本/已粘贴** → 直接扫描。
- **PDF / DOCX / 图片** → 先取文本;阿语扫描件常见乱码,**不可信赖直接抽取**,应将页面光栅化后视觉读取(`pdftoppm -jpeg -r 200 source.pdf page`,逐页 view),再扫描。数字、合同编号、Hijri 日期必须与渲染页面核对(双向文本易错位)。
- 一次喂入多份时,逐份处理、统一汇总(见第三节)。

---

## 一、运行指令(给执行模型)

1. **逐份处理**。先抽取文档标识:`合同编号/文件编号`、`文件类型`、`日期`、`当事方`、`语言`(ZH/EN/AR)。抽不到编号时填"未标注",并记录文件名/首行可辨识信息。
2. **三语逐类比对**下方 R1–R13 风险类别。每类都列出 🔎ZH / 🔎EN / 🔎AR 三组触发词,**任一语言命中即生成一行记录**;不得因文件是英文或阿文就漏扫某类。
3. **引文必须 verbatim**,保留原文语言、原始数字与币种,**不得改写、不得四舍五入**(数字位完整性按 `/arabic-legal-translation` 纪律处理,如混淆 472674986/472674987 这类即属严重错误)。非中文引文后附≤15字中文意译(标注"意译")。
4. **精确定位**:合同给条款号/第X条/附件X;邮件给日期+发件人+主题;信函给段落/页码。
5. **不臆断**。文本未明示而需推断的,严重度降一级并标"〔推断〕";语义模糊的标"〔需复核〕"。
6. **大批量**:每份出行后追加进总表;分批运行时沿用同一表头,末尾累计汇总。
7. 输出语言:**中文**,类别名用中/AR/EN 三语标签。

---

## 二、风险类别与三语触发词清单(筛查核心)

> 每类:含义 → 🔎ZH / 🔎EN / 🔎AR 三语触发词。阿语词为起草辅助,正式认定请对照团队 glossary 复核(见第五节)。

**R1 掠夺性定价 / التسعير المفترس / Predatory pricing**(第6条)
含义:低于成本销售、以亏损换市场份额、意图挤出竞对。
- 🔎ZH:低于成本、亏损换市场、补贴抢份额、打掉/挤出竞对、持续低价直到对手退出、收回亏损
- 🔎EN:below cost, sell at a loss, loss-leading, predatory pricing, undercut, drive out / squeeze out competitors, recoup losses, market share at any cost, price war
- 🔎AR:البيع بأقل من التكلفة، إغراق، الاستحواذ على الحصة بالخسائر، إخراج المنافسين، خسائر مؤقتة لطرد المنافسين، التكلفة المتغيرة المتوسطة (AVC)، إجمالي التكلفة (ATC)
- 点名 HungerStation / Jahez / The Chefz 等竞对的排挤意图尤其高危。

**R2 误导性定价 / التسعير المضلل / Misleading pricing**
含义:虚标原价/划线价、先涨后降、虚假折扣。
- 🔎ZH:虚标原价、划线价、先涨后降、虚假"省XX%"、不实参考价
- 🔎EN:inflated / fake reference price, strikethrough price, "was/now", misleading list price, fake "save X%", deceptive RRP
- 🔎AR:سعر مرجعي وهمي/مضلل، سعر مشطوب، رفع السعر قبل التخفيض، خصم وهمي

**R3 交叉补贴 / الدعم المتبادل / Cross-subsidization**
含义:以某业务/品类/关联实体利润填补另一处低价。
- 🔎ZH:用A补贴B、集团/母公司资金支持终端折扣、利润转移覆盖低价、关联转移定价
- 🔎EN:cross-subsidy / cross-subsidize, fund the discount from, subsidized by, group/parent will absorb, intercompany funding, underwrite the loss, transfer pricing
- 🔎AR:الدعم المتبادل، تمويل الخصومات من نشاط آخر، دعم من الشركة الأم/المجموعة، تحويل الأرباح لتغطية البيع بأقل من التكلفة، التسعير التحويلي بين الشركات

**R4 横向协议 / الاتفاقات الأفقية / Horizontal agreements**(第5条)
含义:竞争者层面的最惠待遇、价格/条件协同、对客户群歧视(排他 exclusionary 或包容 inclusionary)。
- 🔎ZH:与竞对统一价格、瓜分市场/客户、最惠待遇(对竞对)、歧视性条件
- 🔎EN:most favored nation (MFN), most favoured customer, price parity, no less favorable than, price coordination, market sharing, discriminatory terms
- 🔎AR:بند الأفضلية / المعاملة الأكثر تفضيلاً تجاه المنافسين، تنسيق الأسعار، تقاسم الأسواق، معاملة تمييزية

**R5 自我优待 / التفضيل الذاتي / Self-preferencing**(第6条)
含义:平台/渠道对自有品牌或自营商品在排序、曝光、入仓、流量、结算上的优待。
- 🔎ZH:优先展示自营、自有品牌优先排序、向自家商品倾斜流量/展示位
- 🔎EN:self-preferencing, preferential placement / ranking, prioritize our own products, own-brand / house-brand priority, favor our listings, buy-box preference
- 🔎AR:تفضيل المنتجات الخاصة، أولوية في الترتيب/الظهور، أفضلية المنصة لعلامتها الخاصة

**R6 独家供货 / التوريد الحصري / Exclusive supply**(第5/6条)
含义:供应商不得向竞对供货;或以向我方独家供货为条件给予更优价格/资源/合作条件。
- 🔎ZH:供应商不得向竞争对手供货、以独家供货为条件、独家供应商、独家分销
- 🔎EN:exclusive supply, shall not supply to competitors, exclusivity, sole supplier, in consideration of exclusive supply, exclusive distribution
- 🔎AR:عدم التوريد للمنافسين، التوريد الحصري لنا، شرط الحصرية، مورّد حصري، التوزيع الحصري
- 〔justification 提示〕**联合品牌(co-branded)产品的独家销售有合理解释空间** → 标"双面/需归集背景";**仅针对单一品类或单一型号的独家** → 一般不构成,降为低风险。

**R7 最优惠/最惠待遇条款 / بند الأفضلية / Best-price (supplier MFN)**(第5/6条)
含义:要求供应商给我方最优价格/条件;或间接达到同等效果(典型:与竞对价格挂钩的毛利保障——竞对降价、我方跟价,损失由供应商补齐)。
- 🔎ZH:供货价/条件最优、不劣于任何渠道、跟价损失由供应商补足、毛利/利润保障
- 🔎EN:best price clause, most favoured customer, price parity, no less favorable, not higher than offered to any other channel, margin protection / guarantee, price-match make-whole, rebate to cover the shortfall
- 🔎AR:أفضل سعر، لا يقل تفضيلاً عن أي قناة، مطابقة أسعار المنافسين، ضمان هامش الربح، تعويض الخسائر الناتجة عن مطابقة الأسعار

**R8 转售价格维持 / فرض أسعار إعادة البيع / RPM**(第5条)
含义:固定零售价、限定最低零售价。**"价格"含**价格水平、价格变动幅度、利润水平、折扣、手续费等一切价格相关要素——任一被锁定即命中。
- 🔎ZH:固定零售价、最低零售价、不得低于X价、锁定毛利/折扣/手续费水平
- 🔎EN:resale price maintenance (RPM), minimum resale price, minimum advertised price (MAP), shall not resell below, fixed retail price, maintain margin / discount / fee levels
- 🔎AR:فرض أسعار إعادة البيع، الحد الأدنى لسعر البيع، عدم البيع بأقل من، تثبيت الهامش/الخصم/الرسوم

**R9 销售区域/客户限制 / تقييد المنطقة والعملاء / Territory & customer restriction**(第5条)
含义:限制销售区域、限制可销售的客户对象。
- 🔎ZH:限制销售区域、不得跨区销售、限定客户、分配客户、独家区域
- 🔎EN:territorial restriction, shall not sell outside [territory], restricted / allocated customers, exclusive territory, field-of-use restriction
- 🔎AR:تقييد منطقة البيع، حظر البيع خارج المنطقة، تخصيص العملاء، منطقة حصرية

**R10 奖惩与价格/独家挂钩 / ربط المكافآت بالأسعار/الحصرية / Reward-penalty tying**(第5条)
含义:奖惩**挂钩零售价高低、是否独家等正常销售行为** → 高危;**仅挂钩真实违规** → 低风险(标"可抗辩")。
- 🔎ZH:奖励/罚款与零售价挂钩、与是否独家挂钩、不与竞对合作给返点
- 🔎EN:bonus / penalty tied to retail price, rebate conditional on exclusivity, incentive for not dealing with competitors, chargeback for price reductions
- 🔎AR:مكافأة/غرامة مرتبطة بسعر التجزئة، حافز مقابل الحصرية، خصم مشروط بعدم التعامل مع المنافسين

**R11 数据共享 / مشاركة البيانات / Data sharing**
含义:要求供应商交出其他渠道销售数据;或供应商要求我方分享其他供应商产品的销售数据。
- 🔎ZH:供应商分享其他渠道销售数据、交换价格数据、我方分享他商数据
- 🔎EN:share sales data from other channels, competitor sales data, report other suppliers' sales, data exchange
- 🔎AR:مشاركة بيانات المبيعات من قنوات أخرى، بيانات مبيعات المنافسين، تبادل بيانات الأسعار

**R12 附属限制条款 / القيود الملحقة / Ancillary restraints**(第5条)
含义:互不挖人、禁止接受竞对投资、限制新品开发等限制竞争的附属约定。
- 🔎ZH:互不挖人/不挖角、禁止接受竞对投资、限制开发新品
- 🔎EN:no-poach / no-hire, non-solicitation of employees (inter-firm), shall not accept investment from competitors, restriction on new product development
- 🔎AR:عدم استقطاب الموظفين، حظر قبول استثمار من المنافسين، تقييد تطوير منتجات جديدة

**R13 促销折扣/联合营销 / الخصومات الترويجية والتسويق المشترك / Promotional discounts & joint marketing**(**双面**)
- 🔵**有利方向**:供应商**主动**提供采购折扣并要求我方在终端落实促销;双方约定**联合营销/推广** → 归集为**支持低价/促销合理性的有利证据**。
  - 🔎ZH:供应商主动提供折扣并要求做终端促销、双方联合营销/共担推广 ｜ 🔎EN:supplier offered / provided a rebate and requested the in-store promotion, joint / co-op marketing, market development funds (MDF) ｜ 🔎AR:قدّم المورّد خصماً وطلب تنفيذ العرض الترويجي، اتفاق تسويق مشترك، أموال تطوير السوق
- 🔴**风险方向**:**我方要求**供应商出折扣搞促销、或**要求**供应商参与我方组织的联合营销 → 可能构成"施加不合理交易条件"。
  - 🔎ZH:我方要求供应商提供折扣/参与我方营销活动、强制供应商分摊推广 ｜ 🔎EN:we require the supplier to fund the promotion, supplier must contribute to our marketing, mandatory participation in our campaign ｜ 🔎AR:مطالبة المورّد بتمويل العرض الترويجي، إلزام المورّد بالمشاركة في حملتنا التسويقية
- 同一条款两面都沾的,两栏都标。

---

## 三、输出格式

### A. 顶部汇总(每批次结尾刷新)
- 已筛文档数 / 命中文档数
- 按语言(ZH/EN/AR)命中分布
- 按类别(R1–R13)命中计数
- 按严重度计数:🔴高 / 🟡中 / 🟢低 / 🔵有利证据
- **最高风险文档 Top 清单**(列合同编号)
- **有利证据文档清单**(供抗辩归集,列合同编号)

### B. 逐条明细表

| 合同/文件编号 | 类型 | 语言 | 风险类别 | 严重度 | 性质 | 引文(原文,verbatim) | 定位 | 风险说明(挂第几条/哪个方向) | 建议处置 |
|---|---|---|---|---|---|---|---|---|---|

- **严重度**:🔴高 / 🟡中 / 🟢低
- **性质**:风险 / 双面 / 🔵有利(可作抗辩证据)
- **建议处置**:提交律师复核 / 归集背景资料 / 大概率可抗辩 / 有利证据-入抗辩卷

### C. 示例行(说明格式,非真实数据)

| KMT-SUP-2026-0042 | 供货合同 | AR | R7 最优惠条款 بند الأفضلية | 🔴高 | 风险 | "يلتزم المورّد بتعويض الخسائر الناتجة عن مطابقة أسعار المنافسين"(意译:供应商须补偿因跟随竞对价格产生的损失) | 第8.3条 | 与竞对价格挂钩的毛利保障,间接达成最惠效果,触第5条 | 提交律师复核 |
| KMT-SUP-2026-0107 | 邮件 | EN | R13 促销/联合营销 | 🔵有利 | 有利 | "supplier offered an additional 8% rebate and asked us to run the in-store promotion" | 2026-03-14, A.Khan→supplier, 主题 "Ramadan promo" | 折扣由供应商主动提供并要求终端促销,支持低价合理性 | 有利证据-入抗辩卷 |
| KMT-SUP-2026-0061 | 经销合同 | ZH | R8 转售价格维持 RPM | 🟡中 | 风险 | "经销商在任何渠道的零售价不得低于建议零售价的95%" | 第5.2条 | 设定最低转售价,触第5条纵向价格限制 | 提交律师复核 |

---

## 四、纪律红线
- 引文一字不改、数字位完整;不确定的标〔需复核〕而非猜测。
- 三语对等扫描,英文/阿文文件不得漏扫任何一类。
- 只识别、只定位、只归类,**不建议修改/删改/销毁文件**。
- 全程视为 privileged work product;输出抬头注明保密。

---

## 五、阿语术语锁定表(与 `/arabic-legal-translation` 词表核对)

> 这是工作译法,非宣誓翻译。下表区分"已锁定"(与团队 glossary / GAC 惯用语一致,可直接用)与"待标准化"(glossary 未收录,已给可辩护译法,建议正式定稿前对照 GAC 决定书/指南,由 Saudi counsel 终核)。

### A. 已锁定(glossary 一致)

| 概念 | 锁定阿语 | EN |
|---|---|---|
| 竞争总局 | الهيئة العامة للمنافسة | GAC |
| 滥用市场支配地位 | إساءة استخدام الوضع المهيمن | abuse of dominant position |
| 市场支配地位 | الوضع المهيمن / الهيمنة | dominant position / dominance |
| 掠夺性定价 | التسعير المفترس | predatory pricing |
| 低于成本销售 | البيع بأقل من التكلفة | selling below cost |
| 平均可变成本 / 平均总成本 | التكلفة المتغيرة المتوسطة / إجمالي التكلفة | AVC / ATC |
| 交叉补贴 | الدعم المتبادل | cross-subsidization |
| 罚款 | الغرامة | penalty / fine |
| 第六条 | المادة (٦) | Article 6 |

### B. 待标准化(glossary 未收录,可辩护译法,需终核)

| 概念 | 暂用阿语 | 备注 |
|---|---|---|
| 误导性定价 | التسعير المضلل | 与"误导性信息"第二指控区分 |
| 横向协议 | الاتفاقات الأفقية | 第5条;对照决定书措辞 |
| 最惠/最优惠待遇 | بند الأفضلية / بند العميل الأفضل | 已弃用贸易法 شرط الدولة الأولى بالرعاية |
| 自我优待 | التفضيل الذاتي | 较新概念,GAC 指南若有官方表述以其为准 |
| 独家供货 | التوريد الحصري / التعامل الحصري | — |
| 转售价格维持 | فرض أسعار إعادة البيع | — |
| 区域/客户限制 | تقييد منطقة البيع / تخصيص العملاء | — |
| 数据共享 | مشاركة البيانات | — |
| 附属限制条款 | القيود الملحقة | non-poach / 禁投资 / 限新品 |
| 联合营销 | التسويق المشترك | — |

> 注:本筛查器是三语**关键词命中**工具,A、B 两类词均已写入上文 R1–R13 的 🔎AR 触发组——B 类不影响扫描命中,仅影响正式文书定稿用词。
