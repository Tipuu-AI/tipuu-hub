# Tipuu Companion Import（Codex 本地导入）

## 目标
用于在 Tipuu 领取页优先从本地 Codex 宠物目录扫描宠物，回传 `pet.json` 与 `spritesheet`，
并通过消息通道提交到领取页。

## 快速开始
1. 在 Codex 控制台粘贴并执行：
   ```
   tipuu-companion-import --callback-url "https://你的域名/local-import.html?source=claim" --channel tipuu-local-import
   ```
2. 按提示在本地 Codex 目录中选择要带入的宠物。
3. 领取页会自动接收候选文件并提交。

## 参数
- `--callback-url`（可选）
  - 返回回调地址，必须包含 `source=claim`。
  - 示例：`https://你的域名/local-import.html?source=claim`
  - **默认值推导（省略时按顺序尝试）**：
    1. 读取环境变量 `TIPUU_ORIGIN`（如 `https://tipuu.ai`）。
    2. 读取全局配置 `~/.tipuu/config.json` 的 `origin` 字段。
    3. 交互式提示用户输入 origin。
    4. 以上都没有则报错退出，避免回传到错误地址。
  - **推荐做法**：由 Tipuu 领取页在生成提示词时直接把完整 URL 写入命令（包含 `window.location.origin`），用户无需关心推导。
- `--channel`（可选）
  - 默认值：`tipuu-local-import`
  - 领取页需监听相同通道才能接收消息。
- `--pet-id`（可选）
  - 指定要导入的宠物 ID。省略时列出所有可用宠物让用户交互选择。

## 约束
- 仅扫描 `~/.codex/pets/*` 目录。
- 只处理本地宠物目录，不会上传除 `pet.json` 与 `spritesheet` 以外的数据。
- 只回传：
  - `manifestFile`
  - `spritesheetFile`

## 回传事件
- 事件类型：`candidate-selected`
- 通过 `tipuu-local-import` 通道发送。

## 错误与失败场景
- 缺失 `pet.json` 或 `spritesheet`。
- JSON 解析失败。
- 回传超时。
- 目标通道不可达。

## 降级方案
- 本地 skill 不可用时，保留“手动上传”兜底流程。
- 你可在页面手动上传 `pet.json` 与 `spritesheet`。
