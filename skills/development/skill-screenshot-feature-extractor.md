---
id: skill-screenshot-feature-extractor
type: skill
name: screenshot-feature-extractor
description: 'Analyze product screenshots to extract feature lists and generate development
  task checklists. Use when: (1) Analyzing competitor product screenshots for feature
  extraction, (2) Generating PRD/task lists from UI designs, (3) Batch analyzing multiple
  app screens, (4) Conducting competitive analysis from visual references.'
category: development
complexity: medium
keywords: []
capabilities: []
token_estimate: 600
has_references: true
reference_count: 1
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~600 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# screenshot-feature-extractor

> Analyze product screenshots to extract feature lists and generate development task checklists. Use when: (1) Analyzing competitor product screenshots for feature extraction, (2) Generating PRD/task lists from UI designs, (3) Batch analyzing multiple app screens, (4) Conducting competitive analysis from visual references.

# Screenshot Analyzer (Multi-Agent)

Extract product features from UI screenshots using a coordinated multi-agent analysis pipeline.

**Core principle**: Describe WHAT to build (features/interactions), NOT HOW (no tech stack).

## Multi-Agent Architecture

This skill orchestrates 5 specialized agents for comprehensive analysis:

```
                    ┌─────────────────┐
                    │   Coordinator   │
                    │   (this skill)  │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
         ▼                   ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│  UI Analyzer    │ │  Interaction    │ │   Business      │
│  (parallel)     │ │   Analyzer      │ │    Analyzer     │
│                 │ │  (parallel)     │ │   (parallel)    │
└────────┬────────┘ └────────┬────────┘ └────────┬────────┘
         │                   │                   │
         └───────────────────┼───────────────────┘
                             ▼
                    ┌─────────────────┐
                    │   Synthesizer   │
                    │   (sequential)  │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │    Reviewer     │
                    │   (sequential)  │
                    └─────────────────┘
```

## Process

### Phase 1: Screenshot Collection

Gather all screenshots to analyze:
1. Read the screenshot file(s) provided by the user
2. For each screenshot, note the file path and any context provided
3. If multiple screenshots, determine if they are from the same product

### Phase 2: Parallel Analysis

Launch THREE Task agents IN PARALLEL for each screenshot:

**Agent 1: screenshot-ui-analyzer**
```
Analyze this screenshot for UI components, layout structure, and design patterns.
Screenshot: [file path]
Return your analysis as JSON.
```

**Agent 2: screenshot-interaction-analyzer**
```
Analyze this screenshot for user interactions, navigation flows, and state transitions.
Screenshot: [file path]
Return your analysis as JSON.
```

**Agent 3: screenshot-business-analyzer**
```
Analyze this screenshot for business functions, data entities, and domain logic.
Screenshot: [file path]
Return your analysis as JSON.
```

**IMPORTANT**: Use the Task tool with THREE parallel calls in a single message to maximize efficiency.

### Phase 3: Synthesis

After all parallel analyses complete, launch the synthesizer agent:

**Agent 4: screenshot-synthesizer**
```
Synthesize these analysis results into a unified development task list.

UI Analysis:
[paste UI analyzer result]

Interaction Analysis:
[paste Interaction analyzer result]

Business Analysis:
[paste Business analyzer result]

Product Name: [product name]
Output file: docs/plans/YYYY-MM-DD-<product>-features.md
```

### Phase 4: Review

Launch the reviewer agent to validate the output:

**Agent 5: screenshot-reviewer**
```
Review this task list for completeness and quality.

Original screenshot(s): [file paths]
Task list: [synthesized output]

If issues found, provide corrections.
```

### Phase 5: Output

1. Write final task list to `docs/plans/YYYY-MM-DD-<product>-features.md`
2. Use format from [references/output-format.md](references/output-format.md)
3. Present summary to user

## Key Guidelines

- Use `- [ ]` checkbox format for all tasks
- Break features into small, executable subtasks
- Focus on user interactions, not implementation details
- For multiple screenshots: deduplicate features across all screens
- For competitive analysis: highlight unique features and gaps

## Benefits of Multi-Agent Approach

1. **Thoroughness** - Three specialized perspectives catch more details
2. **Speed** - Parallel analysis reduces total time
3. **Quality** - Synthesis + Review ensures coherent, complete output
4. **Specialization** - Each agent focuses on its domain expertise


---


## 📚 Reference Materials


### Output Format

# Output Format Reference

## Required Structure

```markdown
# [产品名称] 开发任务清单

## 项目概述
[一段话描述产品定位和核心功能]

---

## 任务拆解

### 1. [模块名称]
- [ ] [主任务描述 - 功能层面]
  - [ ] [子任务1 - 描述要实现什么功能/交互]
  - [ ] [子任务2 - 描述要实现什么功能/交互]
  - [ ] [子任务3 - 描述要实现什么功能/交互]

### 2. [模块名称]
- [ ] [主任务描述]
  - [ ] [子任务1]
  - [ ] [子任务2]

（继续拆解所有从截图中识别到的功能模块）
```

---

## Task Description Examples

### Good (功能描述)
- [ ] 实现用户注册功能，支持邮箱和手机号两种方式
- [ ] 创建登录页面，包含记住密码和忘记密码选项
- [ ] 设计音频设备选择下拉框，显示所有可用设备
- [ ] 实现实时字幕双语对照显示
- [ ] 添加会议录音功能，支持暂停和继续
- [ ] 创建分享弹窗，包含二维码和链接复制

### Bad (包含技术实现 - 避免)
- [ ] 使用 JWT Token 实现登录态管理
- [ ] 集成 WebSocket 实现实时通信
- [ ] 使用 React 创建组件
- [ ] 调用讯飞 ASR API
- [ ] 用 Redis 缓存数据

---

## Module Organization

Typical modules for web applications:

1. **用户认证与权限** - 注册、登录、权限管理
2. **核心业务功能** - 从截图识别的主要功能
3. **数据展示与管理** - 列表、搜索、筛选、编辑
4. **实时交互功能** - 实时更新、通知、状态同步
5. **分享与协作** - 分享、导出、协作
6. **设置与配置** - 用户偏好、系统设置
7. **付费与会员** - 套餐、支付、账单
8. **管理后台** - 数据统计、用户管理

---

## UI Patterns to Recognize

- 仪表盘布局 (侧边栏、顶部导航、卡片)
- 数据表格 (筛选、排序、分页)
- 表单模式 (多步骤、验证、自动保存)
- 弹窗和抽屉交互
- 空状态和引导流程
- 错误和加载状态
- 响应式布局

---

## Feature Categories

- **Authentication**: 登录、注册、单点登录、双因素认证、密码重置
- **User Management**: 个人资料、角色、权限、团队
- **Content**: 增删改查、富文本、媒体上传、版本控制
- **Collaboration**: 评论、分享、实时协作
- **Analytics**: 数据面板、报表、图表、导出
- **Settings**: 偏好设置、通知、集成
- **Billing**: 套餐、支付、发票、使用量




---

## 🚀 Usage

**Reference this template:** `@skill-screenshot-feature-extractor.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
