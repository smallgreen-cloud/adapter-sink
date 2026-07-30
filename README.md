# adapter-sink

> [Sink](https://github.com/miantiao-me/Sink)（短網址＋點擊統計，6.9k★）的 SmallGreen 適配 repo（Path C）。

[![conformance](https://github.com/smallgreen-cloud/adapter-sink/actions/workflows/conformance.yml/badge.svg)](https://github.com/smallgreen-cloud/adapter-sink/actions/workflows/conformance.yml)

**驗證等級：Discovered**（收錄 ≠ 驗證；晉級條件見 [spec](https://github.com/smallgreen-cloud/spec)）

## 這是什麼

上游程式碼不在本 repo。這裡只放讓 Sink 可被標準化部署與驗證的最小資產：

| 檔案 | 作用 |
|---|---|
| [UPSTREAM.md](UPSTREAM.md) | 鎖定上游 commit（與契約一致性由 CI 驗證） |
| [.smallgreen/](.smallgreen/) | 契約三檔：判定／驗收／維護（含 secrets manifest 與資料流向揭露） |
| [AGENTS.md](AGENTS.md) | 交給你自己的 coding agent 的部署引導 |
| [patches/](patches/) | 對上游的最小修補（目前為空） |

CI 每次依鎖定 commit 組裝「上游＋契約」後跑 [conformance](https://github.com/smallgreen-cloud/conformance)。徽章屬於本適配 repo，不代表上游官方認可。

## 資料流向（信任揭露）

短鏈與統計資料全存部署者自己帳號的 D1／KV／R2。Worker 對外連線僅三類，全部列於 [profile.yaml](.smallgreen/profile.yaml)：自己帳號的 Cloudflare Analytics API（選配統計）、使用者輸入的目標網址（建鏈預覽）、選配 webhook／DoH。無遙測。

## License

AGPL-3.0（隨上游；本 repo 之契約檔與文件亦以相同授權釋出以免混淆）。
