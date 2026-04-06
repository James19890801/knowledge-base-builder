---
name: knowledge-base-builder
description: 帮助用户构建个人知识库的完整方法论。当用户说"帮我建知识库"、"整理我的知识资产"、"扫描我的文件"、"构建知识库"时触发。核心流程：全盘扫描资产 → 建立三层架构（raw/wiki/schema） → 持续归档机制。基于Karpathy方法论，实现"一次构建，持续累积"的知识管理系统。
---

# 知识库构建 Skill

## 核心理念

> **"知识不是存着看的，是在生产中被消费并转化为产出"**

基于Andrej Karpathy的LLM Wiki方法论，帮助用户构建**持续累积**的个人知识库。

与一次性整理的区别：
| 传统知识管理 | 本Skill方法 |
|--------------|-------------|
| 一次性整理 | 持续摄入 |
| 人手动分类 | LLM自动编译 |
| 存着看 | 生产时调用 |
| 无累积效应 | 每次探索都叠加 |

---

## 执行流程

### 阶段一：全盘资产扫描

**扫描范围**：C盘、D盘、E盘... 所有磁盘

**扫描目标**：
- 文档类：*.md / *.docx / *.txt / *.pdf
- 代码类：*.py / *.js / *.html
- 配置类：SKILL.md / CLAUDE.md
- 其他：*.drawio / *.xlsx / *.pptx

**扫描命令**（Windows PowerShell）：
```powershell
# 扫描所有磁盘的Markdown文件
Get-PSDrive -PSProvider FileSystem | ForEach-Object {
    Get-ChildItem -Path "$($_.Root)" -Recurse -Include "*.md" -ErrorAction SilentlyContinue
}
```

**扫描输出**：生成资产清单报告

---

### 阶段二：建立三层架构

**原则**：保留用户现有结构，只新建索引层

```
项目根目录/
├── wiki/                    # LLM维护的知识编译层
│   ├── index.md             # 总索引
│   ├── log.md               # 变更日志
│   ├── concepts/            # 概念页
│   ├── entities/            # 实体页
│   ├── topics/              # 主题页
│   └── insights/            # 洞察页
│
├── raw/                     # 只读的原始素材层
│   ├── meetings/
│   ├── articles-external/
│   └── notes/
│
└── CLAUDE.md                # 规则约束文件
```

**关键原则**：
- ✅ 现有目录保持不变
- ✅ wiki/ 只存索引和提炼
- ✅ 原件通过相对路径链接

---

### 阶段三：批量摄入

**摄入流程**：
1. 读取原始文件
2. 提取核心内容（概念/实体/主题/洞察）
3. 更新 wiki/index.md
4. 追加 wiki/log.md

**页面格式规范**：
```markdown
---
title: 页面标题
type: concept | entity | topic | insight
sources:
  - 相对路径/到/源文件.md
related:
  - [[相关页面]]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

---

### 阶段四：持续归档机制 ⭐ 最重要

**触发时机**：
- 创作完成时（公众号文章、PPT、方案）
- 咨询结束时
- Skill开发完成时
- 会议结束后

**AI行为规范**：
1. 判断产出价值（新观点？新案例？可复用？）
2. 主动询问："这个很有价值，是否归档到知识库？"
3. 用户确认后执行摄入
4. 反馈结果："已归档至 wiki/XXX.md"

---

## 与用户的交互约定

### 首次构建时
询问知识库位置、是否全盘扫描，确认后执行。

### 日常使用时
每次创作完成后，AI主动判断价值并询问是否归档。

---

## Schema文件模板（CLAUDE.md）

```markdown
# [用户名]知识库 Schema

## 身份定义
我是[用户名]，[职业定位]。

## 项目结构
三层架构说明...

## 三大操作流程
- Ingest 摄入
- Query 检索  
- Lint 健康检查
```

---

## 交付清单

| 交付物 | 说明 |
|--------|------|
| `wiki/` 目录 | 知识编译层 |
| `raw/` 目录 | 原始素材层 |
| `CLAUDE.md` | 规则约束文件 |
| `wiki/index.md` | 总索引 |
| `wiki/log.md` | 变更日志 |
| **记忆更新** | 持续归档行为写入记忆 |

---

## 注意事项

1. 不要破坏现有结构 — 只建索引，不搬文件
2. 持续比完美重要 — 先开始，再优化
3. AI主动询问 — 不是用户记得归档，是AI提醒归档
4. 关联比存储重要 — wiki的核心是[[链接]]

---

## 参考来源

- Andrej Karpathy的LLM Wiki方法论
- Angel Capaci的实践分享
