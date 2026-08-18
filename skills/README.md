# Skills（技能）

Agent/Codex 可执行技能目录。每个技能是一个独立目录，包含：

- `skill.md` - 技能定义（Codex 读取的主文件）
- `README.md` - 人类可读文档
- `manifest.json` - 元数据（名称、版本、参数、依赖）
- `examples/` - 使用示例

## 可用技能

| 技能 | 版本 | 说明 |
|------|------|------|
| [companion-import](./companion-import/) | 1.0.0 | 从本地 Codex 目录导入宠物 |

## 使用方式

### 方式 1：通过 Codex 执行

```bash
# 在 Codex 控制台粘贴技能命令
# 具体命令参考各技能的 skill.md
```

### 方式 2：程序化调用

```javascript
// 读取 skill 定义
const skillUrl = 'https://raw.githubusercontent.com/Tipuu-AI/tipuu-hub/main/skills/companion-import/skill.md';
const skillDef = await fetch(skillUrl).then(r => r.text());

// 解析 manifest
const manifestUrl = 'https://raw.githubusercontent.com/Tipuu-AI/tipuu-hub/main/skills/companion-import/manifest.json';
const manifest = await fetch(manifestUrl).then(r => r.json());
```

## 技能开发指南

创建新技能的步骤：

1. 在 `skills/` 下创建新目录：`skills/my-skill/`
2. 编写 `skill.md` - 技能定义和说明
3. 编写 `manifest.json` - 元数据（参考 companion-import 的 manifest）
4. 编写 `README.md` - 详细文档
5. 添加 `examples/` - 使用示例
6. 更新根目录 `index.json` - 注册新技能

### manifest.json 规范

```json
{
  "name": "skill-name",
  "version": "1.0.0",
  "description": "技能描述",
  "author": "作者",
  "tags": ["tag1", "tag2"],
  "parameters": {
    "param-name": {
      "type": "string|number|boolean",
      "required": true|false,
      "default": "default-value",
      "description": "参数说明"
    }
  },
  "constraints": ["约束条件1", "约束条件2"],
  "events": {
    "event-name": {
      "channel": "channel-name",
      "payload": ["field1", "field2"]
    }
  }
}
```

## 提交新技能

1. Fork 本仓库
2. 创建技能目录和文件
3. 更新 `index.json`
4. 提交 Pull Request
