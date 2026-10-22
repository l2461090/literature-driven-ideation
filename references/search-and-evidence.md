# 检索与证据规则

## 选择来源

按学科和实际工具选择数据库或搜索入口；先核对当前连接器的支持范围。

| 方向 | 可考虑的检索入口与核查来源 |
|---|---|
| 跨学科 | 可用的学术检索连接器、OpenAlex、Crossref、Semantic Scholar、出版社文章页 |
| 化学/材料 | ACS、RSC、Wiley、Elsevier、Springer Nature 等出版平台；有权限时用 Web of Science、Scopus、SciFinder；按需加入 ChemRxiv |
| 生物/医学 | PubMed、Europe PMC、相关出版社、适用预印本平台 |
| 计算机/工程 | arXiv、会议官方论文集、ACM、IEEE、相关期刊 |

以上是路由建议，不代表当前环境拥有这些服务的 API 或订阅。网页检索可使用出版社域名限定来发现论文，实际打开文章页核查。不要通过批量抓取登录页或绕过访问控制获取全文。

Crossref/DOI 元数据用于身份核验，不是全文证据。预印本标明状态；发现有正式版本时核对差异。更正或撤稿信息影响证据使用时应保留并解释。

## 两轮检索与候选反向检索

首轮覆盖直接交叉、两个领域瓶颈和经典线索。第二轮扩展新发现术语、追踪核心论文参考文献与引用者、检索相反结果。近期无年份限制的索引检索可补充尚未被年份筛选收录的工作，报告使用的实际截止日期。

每个拟入选课题单独执行反向检索：

1. 搜索“对象＋候选机制/功能”，放宽方法词，寻找常规方法已实现的相同结果。
2. 搜索“候选方法＋目标结果/同义表述”，放宽对象词，寻找邻近材料或体系的先例。
3. 对最近似工作追溯前后向引文及后续改进，比较研究问题、方法、结果、条件和局限。

对于窄领域，说明最近似工作只是间接先例；它不能被描述成已有的直接交叉成果。

停止扩展的参考：主要路线已有代表性工作，近期与经典线索均已检查，且连续两轮扩展未改变候选判断。达到时间/工具限制时也可停止，但需报告剩余盲区，不声称穷尽。

## 可追溯文件

`search-log.jsonl` 每行一个真实操作，字段建议：

```json
{"searched_at":"ISO-8601 timestamp","route":"direct|bottleneck|adjacent|negative|novelty","candidate_id":null,"channel":"实际使用的工具或网站","query":"实际检索式","filters":{},"status":"ok|empty|failed|unavailable","reported_total":null,"records_inspected":[],"notes":"访问限制或扩展理由"}
```

填真实时间与数据；接口未给总数时保留 null。`candidate_id` 在反向查重时填写课题 ID。失败和权限不足也保留，不能伪造成功记录。

`bibliography.csv` 用标准 CSV 转义，建议列：

```text
paper_id,title,authors,year,journal,volume,pages_or_article_number,doi,url,version,publication_status,identity_status,read_level,fulltext_url,si_status,inclusion,reason
```

- `paper_id`：稳定且唯一，如 `P001`。报告、证据表和书目共用同一 ID。
- `journal`、`volume`、`pages_or_article_number`：从可靠书目或出版社核对，供 Nature 格式引用使用；未知则留空并注明待补。正式卷期与在线日期不同另记说明，不把文章号从 DOI 后缀猜出。
- `identity_status`：verified / unresolved；题名、作者、年份与 DOI/官方页面一致才 verified。
- `read_level`：fulltext+SI / fulltext / abstract / metadata / snippet。按真正读到的内容标注。
- `si_status`：read / available-not-read / unavailable / not-applicable / unknown。
- `inclusion`：core / background / excluded / to-read。排除理由具体记录。

预印本和期刊版使用关联 ID 或明确版本字段；不要计作两次独立支持。

## 观点支持

每条关键证据记录包括：证据 ID、论文 ID、章节/图表/页码或摘要位置、原文报告的发现、适用条件、局限、支持/反驳什么、允许的表述和不能升级成的结论。

以精确定位和转述为主，遵守来源引用限制，不复制大段正文。

全文只读了部分章节时说明范围。摘要能支持“摘要报告了某结果”，不能支持未见的具体实验参数、机制验证或排除竞争解释。搜索摘要片段与元数据只能作为发现线索。

建立候选证据链：

> 已有发现 [P...] → 尚未解释或未验证之处 [P... 与检索记录] → 桥接推断（本轮推测）→ 候选假设 → 可区分预测与实验。

每个课题通常引用 3–5 篇直接相关的依据，包括最近似原始研究和方法先例；这不是配额。若只能找到一两篇，应如实降低证据等级，不补入无关论文。正式推荐的关键动机与方法可行性需有已读全文的支持；仅摘要或间接支持时标为暂定。

## 工具故障

遇到网络错误或限流，按服务提示处理，合理重试后改用其他可用入口。没有可用入口时交付已有证据与未完成检索清单。不要静默用模型记忆替代联网检索。

本 skill 不要求购买第三方检索 API；已有连接可用则使用。若某服务需要费用或新凭证，继续使用可用路径并说明覆盖差异。
