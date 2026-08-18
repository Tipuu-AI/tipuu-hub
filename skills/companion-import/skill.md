# Tipuu Companion Import（Codex 本地宠物导入）

## 你的任务
帮助用户把本地 Codex 宠物目录中的宠物上传到 Tipuu 领取页暂存，供页面预览并确认绑定。

本 skill 没有可执行的命令行程序，而是你（Codex 助手）需要按下面的步骤代为完成。
所有输入（`session-id` / `token` / `upload-url`）由领取页生成，并随用户粘贴的提示词一并提供。

## 输入
- `session-id`（必填）：领取页创建的暂存会话 ID，绑定当前登录用户，10 分钟内有效。
- `token`（必填）：一次性上传令牌，随会话生成，用后即失效，请勿泄露给他人。
- `upload-url`（必填）：服务端上传地址，形如 `https://你的域名/ai-toy/import/upload`。

## 执行步骤
1. 从用户提示词中读取 `session-id`、`token`、`upload-url` 三个值。
2. 扫描本地 `~/.codex/pets/*` 目录，找出每个宠物目录下的 `pet.json`（manifest）与 `spritesheet`（webp/png）。
3. 把扫描到的宠物列表展示给用户，请用户选择要导入哪一个宠物。
4. 用 `curl` 把用户选中的 `pet.json` 与 `spritesheet` 上传到 `upload-url`（字段名固定，不可改动）：

   ```bash
   curl -fsS \
     -F "session_id=<SESSION_ID>" \
     -F "token=<TOKEN>" \
     -F "manifest=@<绝对路径>/pet.json" \
     -F "spritesheet=@<绝对路径>/spritesheet.webp" \
     "<UPLOAD_URL>"
   ```

5. 上传成功（HTTP 2xx 且响应体 `code == 0`）后，**最后一条输出**必须固定为成功文案（见下）。

## 约束
- 仅扫描 `~/.codex/pets/*` 目录。
- 只处理本地宠物目录，只上传 `pet.json` 与 `spritesheet`，不上传其它数据。
- `manifest` 为 JSON 文件，`spritesheet` 为 webp/png。
- 请求类型 `multipart/form-data`，无 JWT，鉴权完全依赖一次性令牌。
- 不要泄露 `session-id` / `token` 给第三方。

## 成功文案（固定，勿改）
上传成功后，**最后一条输出**必须固定为：

```
导入完成，请在浏览器领取页主动刷新一次页面确认
```

## 错误与失败场景
- 缺失 `pet.json` 或 `spritesheet`：提示用户检查文件完整性。
- JSON 解析失败：提示文件格式错误。
- 上传失败（HTTP 非 2xx，如 401 令牌无效/过期、409 会话已消费）：提示重新打开领取页获取新凭据。
- 上传地址不可达：提示检查网络或重新打开领取页。

## 降级方案
- 本地 skill 不可用时，保留领取页的“手动上传”兜底流程。
- 用户可在页面手动上传 `pet.json` 与 `spritesheet`。
