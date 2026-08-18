# Companion Import Skill

Codex 本地宠物导入技能，用于从本地 Codex 目录扫描宠物并导入到 Tipuu。

## 功能

- 扫描 `~/.codex/pets/*` 目录中的本地宠物
- 提取 `pet.json` 和 `spritesheet` 文件
- 通过消息通道回传到 Tipuu 领取页
- 支持指定宠物 ID 和自定义回调地址

## 使用方式

在 Codex 控制台执行：

```bash
tipuu-companion-import --callback-url "https://你的域名/local-import.html?source=claim" --channel tipuu-local-import
```

## 参数说明

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `--callback-url` | string | 否 | - | 回调地址，必须包含 `source=claim` |
| `--channel` | string | 否 | `tipuu-local-import` | 消息通道名称 |
| `--pet-id` | string | 否 | - | 指定要导入的宠物 ID |

## 约束

- ✅ 仅扫描 `~/.codex/pets/*` 目录
- ✅ 只处理本地宠物目录
- ✅ 只回传 `pet.json` 和 `spritesheet`
- ❌ 不会上传其他数据

## 事件

- **事件类型**：`candidate-selected`
- **通道**：`tipuu-local-import`
- **载荷**：`manifestFile` + `spritesheetFile`

## 错误处理

| 场景 | 处理方式 |
|------|---------|
| 缺失 `pet.json` 或 `spritesheet` | 提示用户检查文件完整性 |
| JSON 解析失败 | 提示文件格式错误 |
| 回传超时 | 建议重试或改用手动上传 |
| 目标通道不可达 | 自动降级到手动上传流程 |

## 降级方案

当本地 skill 不可用时：
- 保留"手动上传"兜底流程
- 用户可在页面手动上传 `pet.json` 与 `spritesheet`

## 示例

查看 [examples/](./examples/) 目录获取使用示例。
