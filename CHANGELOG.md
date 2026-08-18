# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [1.0.0] - 2026-08-18

### Added

- **Initial release** of tipuu-hub repository
- **companion-import skill** (v1.0.0)
  - Codex 本地宠物导入技能
  - 支持扫描 `~/.codex/pets/*` 目录
  - 支持自定义回调地址和消息通道
  - 包含完整文档、manifest 和使用示例
- **Repository structure**
  - `skills/` - Agent/Codex 技能目录
  - `docs/` - 用户文档目录
  - `templates/` - 模板资源目录
  - `examples/` - 示例代码目录
  - `assets/` - 公开资源目录
- **index.json** - 内容索引文件，支持工具程序化发现
- **README.md** - 仓库总览和导航

### Migration

- 从主仓 `Ti-social/static/skills-hub/tipuu-companion-import` 迁移为独立 submodule
- 重新组织为根目录 `tipuu-hub/`
- 扁平 `skill.md` 重组为结构化 skill 目录

[1.0.0]: https://github.com/Tipuu-AI/tipuu-hub/releases/tag/v1.0.0
