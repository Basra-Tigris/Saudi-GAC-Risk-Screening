# keemart-gac-risk-screening

三语(中/英/阿)竞争法风险文本筛查 skill,用于批量评审 Keeta / Keemart 的信函、邮件、合同,
比对 GAC 拟立案的调查方向,逐条定位到合同编号 + 具体引文,输出结构化筛查清单。

## 安装
把整个 `keemart-gac-risk-screening/` 文件夹放进你的 skills 目录:
- Claude Code / 插件式 skills:放到 `~/.claude/skills/`(或你的 skills 根目录)下。
- 其他支持 SKILL.md 的环境:置于该环境约定的 skills 路径,确保 `SKILL.md` 在文件夹根部。

加载后按名称 `keemart-gac-risk-screening` 触发,或直接说"用风险筛查器扫这批合同"。

## 用法
把待筛文档(粘贴文本、PDF、DOCX、图片皆可)交给它,它会:
1. 抽取文档标识(合同编号/类型/日期/当事方/语言);
2. 三语逐类比对 R1–R13 风险触发词;
3. 输出顶部汇总 + 逐条明细表(含严重度、性质、verbatim 引文、精确定位、建议处置)。

## 边界
仅作风险识别与证据归集,不建议修改/删改/销毁任何文件;输出按 privileged work product 处理。
阿语术语见 SKILL.md 第五节:A 类已与团队 glossary 锁定,B 类待对照 GAC 决定书/指南终核。
