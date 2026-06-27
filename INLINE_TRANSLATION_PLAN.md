# DeepLX Inline 翻译实现计划

> 分支：`feat/inline-deeplx-translation`
> 状态：规划中（P0 未开始）
> 最后更新：2026-06-27

## 实现进度

- [ ] 扩展 `Translation.ts`：inlineEnabled、deeplx、targetLanguage（持久化，不改 version）
- [ ] 实现 DeepLXClient、TranslationRequestQueue、splitTranslationText、DeepLLanguageMap 及单元测试
- [ ] 实现 MessageTranslationStore、TranslationCommands、MessageTranslationBlock，挂载到 UserMessage.tsx
- [ ] 在翻译设置区新增独立「Inline 翻译」栏（主开关 + DeepLX 子配置 + 测试服务）
- [ ] 改造 TranslateMenuItems、MessageContextMenu、MessageActionMenu：inlineReady 分流
- [ ] 手动验证：开关切换、整句/选中翻译、Show Original、CORS、移动 bottom sheet、外链 fallback

## 目标与约束

- **无 Fluxer 后端**：客户端直接 fetch 用户配置的 DeepLX API URL
- **模式开关**：翻译设置区独立「Inline 翻译」栏；关=外链，开+DeepLX有效=消息下方 inline
- **P0**：手动单条翻译、纯文本、单个 DeepLX 配置
- **平台**：Web + Mobile Web；CORS 失败时错误提示 + 外链 fallback

## 现状与改动面

- `fluxer_app/src/features/messaging/state/Translation.ts` — 现有 urlTemplate 外链
- `TranslateMenuItems.tsx` / `SearchMenuData.tsx` — openExternalUrl
- `buildMessagePlaintextCopyText` — 提取翻译文本
- `UserMessage.tsx` — normal / compact / CLIENT_SYSTEM 三套布局

## 架构

inlineReady ? TranslationCommands.translateInline : openExternalUrl
  -> MessageTranslationStore -> DeepLXClient -> 用户 DeepLX 端点

## 1. 数据模型（Translation.ts）

新增（持久化，不动 engines）：
- inlineEnabled (default false)
- deeplx: { apiUrl, maxRequestsPerSecond: 3, maxTextLength: 1200, maxParagraphs: 1 }
- targetLanguage (null = 跟随 UI locale)
- inlineReady getter

## 2. 设置 UI（SearchEnginesTab）

InlineTranslationContent：总开关 + 展开 DeepLX 子配置 + 测试服务

## 3. 新模块 fluxer_app/src/features/translation/

DeepLXClient, TranslationRequestQueue, splitTranslationText, DeepLLanguageMap,
MessageTranslationStore, TranslationCommands, MessageTranslationBlock

## 4. 菜单

TranslateMenuItems, MessageContextMenu, MessageActionMenu — inlineReady 分流

## 5. 不在 P0

富文本、自动翻译、后端代理、IndexedDB、protobuf

## 6. 实施顺序

1. Translation.ts 2. Client+Queue+tests 3. Store+Commands 4. UI block 5. Settings 6. Menus 7. QA

## 7. 风险

CORS / 多布局挂载 / engines 隔离
