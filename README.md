# Tipuu Hub

Tipuu 官方公开资源仓库，提供技能文档、用户指南、模板资源等，可被工具和用户下载使用。

## 📦 内容索引

### 🎯 Skills（技能）

Agent/Codex 可执行技能，用于自动化特定任务。

| 技能 | 版本 | 说明 | 文档 |
|------|------|------|------|
| [companion-import](./skills/companion-import/) | 1.0.0 | 从本地 Codex 目录导入宠物 | [skill.md](./skills/companion-import/skill.md) |

**使用方式**：
```bash
# Clone 仓库
git clone https://github.com/Tipuu-AI/tipuu-hub.git

# 或在 Codex 中直接引用 skill
# 参考具体 skill 文档
```

### 📖 文档（Docs）

- [快速上手](./docs/getting-started/) - 新用户入门指南
- [使用指南](./docs/guides/) - 详细功能说明
- [API 文档](./docs/api/) - 接口规范
- [常见问题](./docs/faq/) - FAQ 和故障排查

### 📋 模板（Templates）

- [宠物模板](./templates/pet/) - pet.json 示例模板
- [配置模板](./templates/config/) - 配置文件示例

### 💡 示例（Examples）

- [基础示例](./examples/basic/) - 简单使用场景
- [高级示例](./examples/advanced/) - 复杂集成案例

### 🎨 资源（Assets）

- [精灵图](./assets/sprites/) - 宠物动画帧
- [图标](./assets/icons/) - UI 图标
- [媒体](./assets/media/) - 其他媒体文件

## 🔧 工具集成

### 程序化发现内容

仓库根目录提供 `index.json`，工具可解析此文件发现所有内容：

```json
{
  "skills": [
    {
      "name": "companion-import",
      "path": "skills/companion-import",
      "description": "从本地 Codex 目录导入宠物"
    }
  ]
}
```

### 通过 GitHub Raw URL 访问

```bash
# 获取 skill 定义
curl https://raw.githubusercontent.com/Tipuu-AI/tipuu-hub/main/skills/companion-import/skill.md

# 获取 index
curl https://raw.githubusercontent.com/Tipuu-AI/tipuu-hub/main/index.json
```

## 📂 目录结构

```
tipuu-hub/
├── README.md          # 本文件
├── index.json         # 内容索引（工具发现用）
├── CHANGELOG.md       # 版本变更记录
│
├── skills/            # Agent/Codex 技能
│   └── companion-import/
│       ├── skill.md       # 技能定义
│       ├── README.md      # 技能文档
│       ├── manifest.json  # 元数据
│       └── examples/      # 使用示例
│
├── docs/              # 用户文档
│   ├── getting-started/
│   ├── guides/
│   ├── api/
│   └── faq/
│
├── templates/         # 模板资源
│   ├── pet/
│   └── config/
│
├── examples/          # 示例代码
│   ├── basic/
│   └── advanced/
│
└── assets/            # 公开资源
    ├── sprites/
    ├── icons/
    └── media/
```

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

[待添加]

## 🔗 相关链接

- [Tipuu 主项目](https://github.com/Tipuu-AI/Ti-social)
- [问题反馈](https://github.com/Tipuu-AI/tipuu-hub/issues)
