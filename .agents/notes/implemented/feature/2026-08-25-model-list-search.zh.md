# Agent Note: Search in the composer model list

Status: implemented

## Problem

composer 模型列表按提供方分组渲染所有已公布行，因此在一个大目录中挑选一个已知的模型意味着滚动或逐组阅读。模型发现在真正发生选择的地方退化，而验证过需求的动态插件原型（会话级低优先级座位遮蔽）无法成为持久答案：产品 UI 不能依赖进程内的临时扩展。

## Decision

composer 座位钻入的 Model 面板拥有一个搜索框（[会话模型选择器](2026-07-24-web-session-model-selector.md)）。空格分隔的关键词不区分大小写，且必须全部命中提供方名称、提供方 id、模型名称、id 或说明中的某处。Enter 经由不变的共享选择路径提交首个幸存行；ArrowDown 把焦点从输入框移入列表；Escape 先清空非空关键词，再恢复既有的逐级退回路径；被完全过滤空的目录显示带查询词的空态文案，与原有的完全无模型文案并存。进入或重新打开面板都会重置查询。`/model` popupSelect 保持完整不过滤的选项列表。

## Alternatives considered

**由独立包或动态插件遮蔽座位。** 低优先级的第二占位者让 `ui-model-selection` 保持不动，但为同一个功能交付两个相互竞争的触发器，并把 Effort 面板的状态复制一份；作为永久架构被否决，仅用作一次性验证原型。

**同时过滤 `/model` 弹层。** 弹层外壳消费任意贡献方给出的普通选项数组，按条目过滤需要一个尚无包拥有的 command-UI 扩展点；已记录为包 README 的已知限制。

**模糊或打分的客户端匹配。** 必须命中的子串关键词相对适配器自有的名称与 id 保持可解释性；相关性打分在当前没有消费者的情况下引入词汇假设。

## Consequences

大目录从 composer 一次键入即可选中；过滤器直接骑在既有目录 store 上，未移动任何 wire、持久化或 Host 契约。弹层与座位的能力自此不同，包 README 已记录该差异，直至命令 UI 长出过滤接缝。

## Testing

`tests/model-select.client.spec.tsx` 钉住查询过滤、提供方关键词命中、Enter 提交首匹配并关闭、以及先清空再退回的 Escape 阶梯；`DSH_SNAPSHOT=replay pnpm run test:web` 覆盖装配后的浏览器输出。
