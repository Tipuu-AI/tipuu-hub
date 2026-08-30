---
name: companion-import
description: Prepare and upload a local Codex pet from ~/.codex/pets to a Tipuu claim-page import session, including user-guided characterSetting editing.
---

# Tipuu Companion Import

This native Codex entrypoint mirrors the public raw entrypoint in [skill.md](skill.md). Use a natural, patient, friendly tone while helping the user choose a pet and shape its character setting.

## Required Inputs

- `session-id`: temporary claim-page import session ID.
- `token`: one-time upload token for this session.
- `upload-url`: absolute `http://` or `https://` URL whose path ends with `/ai-toy/import/upload`.
- `pet-id` (optional): local directory name under `~/.codex/pets/`.

## Workflow

1. Read `session-id`, `token`, `upload-url`, and optional `pet-id` from the user prompt.
2. Validate `upload-url`; stop if it is not an absolute http(s) URL or does not target `/ai-toy/import/upload`.
3. Scan only `~/.codex/pets/*`.
4. For each candidate pet, require `pet.json` and one webp/png spritesheet inside the same pet directory.
   - Prefer a local relative spritesheet filename declared by `pet.json` in `spritesheet`, `spritesheet.path`, or `spritesheetPath`.
   - Otherwise use `spritesheet.webp` or `spritesheet.png`.
   - Reject absolute paths, `../` paths, and files outside the pet directory.
5. Display every candidate as JSON content plus spritesheet information: directory name, important manifest fields, current `characterSetting` if present, spritesheet path/type/size, and image preview when the environment supports it.
6. If `pet-id` is provided, import only that pet but still show its parsed JSON and spritesheet for confirmation. Otherwise present available pets for selection; if exactly one pet is available, show it and ask for confirmation.
7. Guide the user to finalize a concise `characterSetting` for the selected pet.
   - Draft from the manifest and spritesheet appearance.
   - Cover origin, backstory, strengths, core traits, stable behaviors, and a natural relationship starting point with the user.
   - Keep the interaction warm and close, but keep the final text restrained, clear, and not overly conversational.
   - For recognizable public IP, web research may be used when available, but never send session credentials, local files, or local absolute paths to third parties.
   - Iterate until the user explicitly says the setting is satisfactory.
8. After explicit confirmation, update the selected `pet.json` by adding or replacing only top-level `characterSetting`. Trim whitespace, keep it a string, and keep it within 1500 characters. Preserve every other field and value.
9. Re-read `pet.json` and verify JSON validity, `characterSetting`, and spritesheet existence.
10. Upload the selected files with multipart field names exactly `session_id`, `token`, `manifest`, and `spritesheet`.

```bash
curl -fsS \
  -F "session_id=<SESSION_ID>" \
  -F "token=<TOKEN>" \
  -F "manifest=@<ABSOLUTE_PATH>/pet.json" \
  -F "spritesheet=@<ABSOLUTE_PATH>/<SPRITESHEET_FILE>" \
  "<UPLOAD_URL>"
```

Treat the upload as successful only when HTTP succeeds and the JSON response has `code == 0`. The final assistant message after success must be exactly:

```text
导入完成，请在浏览器领取页主动刷新一次页面确认
```

Do not send `session-id`, `token`, `pet.json`, or spritesheet files to any URL other than the validated `upload-url`.
