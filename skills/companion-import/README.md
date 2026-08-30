# Companion Import Skill

Codex 本地宠物导入技能：引导 Codex 扫描本地 `~/.codex/pets/*` 目录，展示 JSON 内容与精灵图，陪用户挑选并完善角色设定，然后用一次性令牌上传到 Tipuu 领取页暂存，供预览并确认绑定。

## 功能

- 扫描 `~/.codex/pets/*` 目录中的本地宠物
- 提取并展示 `pet.json` 和 `spritesheet` 文件
- 引导用户补充并确认 `characterSetting`
- 将确认后的 `characterSetting` 写回本地 `pet.json`
- 用一次性令牌（session-id + token）通过 HTTP 上传到服务端暂存
- 领取页浏览器轮询会话状态，拿到预览后由用户确认绑定

## 使用方式

领取页会生成一段提示词（含 skill 地址 + 本次会话凭据），用户把它粘贴到 Codex 对话框执行。Codex 下载 skill 后按说明完成：扫描宠物 → 展示 JSON 内容与精灵图 → 让用户选择 → 引导确认角色设定 → 写回 `pet.json` → `curl` 上传。

skill 需要三个由提示词提供的输入：

| 输入 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `session-id` | string | 是 | 领取页创建的暂存会话 ID，绑定当前用户，10 分钟内有效 |
| `token` | string | 是 | 一次性上传令牌，用后即失效，请勿泄露 |
| `upload-url` | string | 是 | 上传地址，形如 `https://你的域名/ai-toy/import/upload` |
| `pet-id` | string | 否 | 本地宠物目录名；提供时只导入 `~/.codex/pets/<pet-id>` |

## 上传方式

选择宠物后，Codex 执行等价命令（字段名固定）：

```bash
curl -fsS \
  -F "session_id=<SESSION_ID>" \
  -F "token=<TOKEN>" \
  -F "manifest=@/绝对路径/pet.json" \
  -F "spritesheet=@/绝对路径/<spritesheet.webp|spritesheet.png>" \
  "<UPLOAD_URL>"
```

请求类型 `multipart/form-data`，无 JWT，鉴权完全依赖一次性令牌。

## 约束

- ✅ 仅扫描 `~/.codex/pets/*` 目录
- ✅ 只处理本地宠物目录
- ✅ 只上传 `pet.json` 和 `spritesheet`
- ✅ 只接受宠物目录内的 webp/png 精灵图；不会跟随绝对路径或 `../` 路径
- ❌ 不会上传其他数据
- ❌ 不会把凭据或文件上传到非 `/ai-toy/import/upload` 的地址

## 精灵图发现规则

Codex 先解析 `pet.json`，优先使用其中声明的本地相对精灵图文件名（如 `spritesheet`、`spritesheet.path` 或 `spritesheetPath`）。如果 manifest 没有声明可用文件，则回退查找同目录下的 `spritesheet.webp` 或 `spritesheet.png`。

## 角色设定写回

上传前，Codex 必须和用户一起确认选中宠物的 `characterSetting`。这段设定写入 `pet.json` 的顶层 `characterSetting` 字段，与 AI-Toy 后端解析字段保持一致。

角色设定应包含来历、背景故事、擅长做的事情、核心特质、稳定行为特征和与用户的关系起点。互动过程应自然、温和、有亲近感；最终文本应简练明确，不过度口语化，且不超过 1500 字符。

写回时只允许新增或更新 `characterSetting`，不得改动其它 JSON 字段和值。

## 成功文案

上传成功后最后一条输出固定为：

```
导入完成，请在浏览器领取页主动刷新一次页面确认
```

## 错误处理

| 场景 | 处理方式 |
|------|---------|
| 缺失 `pet.json` 或 `spritesheet` | 提示用户检查文件完整性 |
| `pet-id` 不存在或目录不完整 | 提示用户检查 `~/.codex/pets/<pet-id>` |
| JSON 解析失败 | 提示文件格式错误 |
| 用户未确认角色设定 | 继续引导定稿，不写文件、不上传 |
| `characterSetting` 超过 1500 字符 | 提示压缩内容后再保存 |
| 上传地址格式不合法或路径不匹配 | 停止上传，提示重新打开领取页 |
| 上传失败（HTTP 非 2xx） | 提示令牌无效/过期或会话已消费，需重新打开领取页 |
| 上传地址不可达 | 提示检查网络或重新打开领取页 |

## 降级方案

当本地 skill 不可用时：
- 保留领取页的"手动上传"兜底流程
- 用户可在页面手动上传 `pet.json` 与 `spritesheet`

## 示例

查看 [examples/](./examples/) 目录获取使用示例。
