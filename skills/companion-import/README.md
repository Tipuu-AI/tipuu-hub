# Companion Import Skill

Codex 本地宠物导入技能：引导 Codex 扫描本地 `~/.codex/pets/*` 目录、让用户挑选宠物，然后用一次性令牌上传到 Tipuu 领取页暂存，供预览并确认绑定。

## 功能

- 扫描 `~/.codex/pets/*` 目录中的本地宠物
- 提取 `pet.json` 和 `spritesheet` 文件
- 用一次性令牌（session-id + token）通过 HTTP 上传到服务端暂存
- 领取页浏览器轮询会话状态，拿到预览后由用户确认绑定

## 使用方式

领取页会生成一段提示词（含 skill 地址 + 本次会话凭据），用户把它粘贴到 Codex 对话框执行。Codex 下载 skill 后按说明完成：扫描宠物 → 让用户选择 → `curl` 上传。

skill 需要三个由提示词提供的输入：

| 输入 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `session-id` | string | 是 | 领取页创建的暂存会话 ID，绑定当前用户，10 分钟内有效 |
| `token` | string | 是 | 一次性上传令牌，用后即失效，请勿泄露 |
| `upload-url` | string | 是 | 上传地址，形如 `https://你的域名/ai-toy/import/upload` |

## 上传方式

选择宠物后，Codex 执行等价命令（字段名固定）：

```bash
curl -fsS \
  -F "session_id=<SESSION_ID>" \
  -F "token=<TOKEN>" \
  -F "manifest=@/绝对路径/pet.json" \
  -F "spritesheet=@/绝对路径/spritesheet.webp" \
  "<UPLOAD_URL>"
```

请求类型 `multipart/form-data`，无 JWT，鉴权完全依赖一次性令牌。

## 约束

- ✅ 仅扫描 `~/.codex/pets/*` 目录
- ✅ 只处理本地宠物目录
- ✅ 只上传 `pet.json` 和 `spritesheet`
- ❌ 不会上传其他数据

## 成功文案

上传成功后最后一条输出固定为：

```
导入完成，请在浏览器领取页主动刷新一次页面确认
```

## 错误处理

| 场景 | 处理方式 |
|------|---------|
| 缺失 `pet.json` 或 `spritesheet` | 提示用户检查文件完整性 |
| JSON 解析失败 | 提示文件格式错误 |
| 上传失败（HTTP 非 2xx） | 提示令牌无效/过期或会话已消费，需重新打开领取页 |
| 上传地址不可达 | 提示检查网络或重新打开领取页 |

## 降级方案

当本地 skill 不可用时：
- 保留领取页的"手动上传"兜底流程
- 用户可在页面手动上传 `pet.json` 与 `spritesheet`

## 示例

查看 [examples/](./examples/) 目录获取使用示例。
