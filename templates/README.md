# 模板（Templates）

可复用的模板资源目录。

## 目录结构

- **[pet/](./pet/)** - 宠物模板
  - `basic-pet.json` - 基础宠物模板
  - `animated-pet.json` - 动画宠物模板

- **[config/](./config/)** - 配置模板
  - `tipuu-config.example.yaml` - Tipuu 配置示例

## 使用方式

```bash
# 复制模板并修改
cp templates/pet/basic-pet.json my-pet.json
vim my-pet.json

# 或使用 curl 下载
curl -O https://raw.githubusercontent.com/Tipuu-AI/tipuu-hub/main/templates/pet/basic-pet.json
```

## 状态

⚠️ **占位目录** - 模板内容待补充
