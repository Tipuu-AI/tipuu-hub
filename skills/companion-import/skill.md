---
name: companion-import
description: Prepare and upload a local Codex pet from ~/.codex/pets to a Tipuu claim-page import session, including user-guided characterSetting editing.
---
# Tipuu Companion Import（Codex 本地宠物导入）

## 你的任务
帮助用户把本地 Codex 宠物目录中的宠物整理成可领取的 Tipuu 伙伴：先展示本地宠物的 JSON 内容与精灵图，陪用户选定宠物并完善角色设定，再把更新后的 `pet.json` 与精灵图上传到 Tipuu 领取页暂存，供页面预览并确认绑定。

本 skill 没有可执行的命令行程序，而是你（Codex 助手）需要按下面的步骤代为完成。
所有输入（`session-id` / `token` / `upload-url`）由领取页生成，并随用户粘贴的提示词一并提供。

## 输入
- `session-id`（必填）：领取页创建的暂存会话 ID，绑定当前登录用户，10 分钟内有效。
- `token`（必填）：一次性上传令牌，随会话生成，用后即失效，请勿泄露给他人。
- `upload-url`（必填）：服务端上传地址，形如 `https://你的域名/ai-toy/import/upload`。
- `pet-id`（可选）：本地宠物目录名；提供时只导入 `~/.codex/pets/<pet-id>`，不再交互选择。

## 执行步骤
1. 从用户提示词中读取 `session-id`、`token`、`upload-url` 三个值，以及可选的 `pet-id`。
2. 校验 `upload-url`：必须是 `http://` 或 `https://` 绝对 URL，且路径必须以 `/ai-toy/import/upload` 结尾。只向该 URL 上传，不向其它第三方地址发送凭据或文件。
3. 扫描本地 `~/.codex/pets/*` 目录，找出每个宠物目录下的 `pet.json`（manifest）与精灵图。
   - 精灵图优先使用 `pet.json` 中声明的本地文件名（如 `spritesheet`、`spritesheet.path` 或 `spritesheetPath`）。
   - 声明缺失或不是本地相对文件名时，查找同目录下的 `spritesheet.webp` 或 `spritesheet.png`。
   - 只接受宠物目录内的 webp/png 文件，不跟随到目录外的路径。
4. 解析并展示候选宠物。每个候选都按“JSON 内容 + 精灵图”的方式展示给用户：
   - 展示宠物目录名、`pet.json` 核心字段（如 `id`、`displayName`/`name`、`description`、`characterSetting`、`spritesheet`、动画摘要）。
   - 展示对应精灵图路径、格式、大小；如果当前环境支持图片查看，应同时展示或预览精灵图。
   - 不展示或记录 `token`，不把本地绝对路径发送给第三方服务。
5. 如果提示词提供了 `pet-id`，只导入对应目录，但仍要先展示该宠物的 JSON 内容与精灵图并请用户确认；否则让用户从候选列表中选择。只有一个可用宠物时也要展示内容并确认。
6. 引导用户为选中的精灵完善角色设定：
   - 先根据 `pet.json` 内容和精灵图外观提出一版 `characterSetting` 草稿。
   - 草稿应覆盖来历、背景故事、擅长做的事情、核心特质、稳定行为特征，以及与用户建立关系的自然起点。
   - 文风应自然、温和、有亲近感，但不要过度口语化、卖萌化或堆砌情绪；内容简练，核心意思明确。
   - 如果角色明显来自公开 IP 或用户要求补充背景，可查询网络公开资料发散，但不要上传 `session-id`、`token`、本地文件或本地绝对路径；不确定的事实用保守表述或请用户确认。
   - 与用户多轮打磨，询问用户是否满意；用户明确确认前不要写文件或上传。
7. 用户确认最终角色设定后，编辑本地 `pet.json`：
   - 将确认文本写入顶层字段 `characterSetting`。
   - 只新增或更新 `characterSetting`，其它 JSON 字段和值必须保持不变。
   - 保存前去除首尾空白，内容必须是字符串且不超过 1500 字符。
8. 写回后重新读取 `pet.json` 验证 JSON 仍合法、`characterSetting` 已存在且为字符串、精灵图仍存在。
9. 用 `curl` 把更新后的 `pet.json` 与 `spritesheet` 上传到 `upload-url`（字段名固定，不可改动）：

   ```bash
   curl -fsS \
     -F "session_id=<SESSION_ID>" \
     -F "token=<TOKEN>" \
     -F "manifest=@<绝对路径>/pet.json" \
     -F "spritesheet=@<绝对路径>/<spritesheet.webp|spritesheet.png>" \
     "<UPLOAD_URL>"
   ```

10. 上传成功（HTTP 2xx 且响应体 `code == 0`）后，**最后一条输出**必须固定为成功文案（见下）。

## 互动氛围
- 整个过程要像陪用户一起把本地伙伴带回 Tipuu：语气自然、耐心、亲近，有轻松的陪伴感。
- 展示候选、提出角色设定草稿和请求确认时，避免机械清单式压迫感；用清晰但温和的表达。
- 角色设定文本本身要克制、凝练、可长期使用，不写成聊天口吻、宣传文案或系统规则。

## 约束
- 仅扫描 `~/.codex/pets/*` 目录。
- 只处理本地宠物目录，只上传 `pet.json` 与 `spritesheet`，不上传其它数据。
- `manifest` 为 JSON 文件，`spritesheet` 为 webp/png。
- 不上传目录外文件，即使 `pet.json` 中声明了绝对路径或 `../` 路径。
- 上传前必须获得用户对最终 `characterSetting` 的明确确认。
- 写回 `pet.json` 时只能新增或更新顶层 `characterSetting` 字段。
- 请求类型 `multipart/form-data`，无 JWT，鉴权完全依赖一次性令牌。
- 不要泄露 `session-id` / `token` 给第三方。

## 成功文案（固定，勿改）
上传成功后，**最后一条输出**必须固定为：

```
导入完成，请在浏览器领取页主动刷新一次页面确认
```

## 错误与失败场景
- 缺失 `pet.json` 或 `spritesheet`：提示用户检查文件完整性。
- `pet-id` 不存在或对应目录不完整：提示用户检查 `~/.codex/pets/<pet-id>`。
- JSON 解析失败：提示文件格式错误。
- 用户未确认角色设定：不要写文件或上传，继续引导定稿。
- `characterSetting` 超过 1500 字符：提示用户压缩内容后再保存。
- `upload-url` 非 http(s) 绝对 URL 或路径不匹配：停止上传，提示重新打开领取页获取新凭据。
- 上传失败（HTTP 非 2xx，如 401 令牌无效/过期、409 会话已消费）：提示重新打开领取页获取新凭据。
- 上传地址不可达：提示检查网络或重新打开领取页。

## 降级方案
- 本地 skill 不可用时，保留领取页的“手动上传”兜底流程。
- 用户可在页面手动上传 `pet.json` 与 `spritesheet`。
