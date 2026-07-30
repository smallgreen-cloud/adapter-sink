# UPSTREAM

| 欄位 | 值 |
|---|---|
| 上游 | [miantiao-me/Sink](https://github.com/miantiao-me/Sink) |
| 鎖定 commit | `3f8d30ae8c917f269249d6c7c1c3894fc8d8af91` |
| commit 日期 | 2026-07-19 |
| License | AGPL-3.0 |
| 鎖定日期 | 2026-07-30 |

## 同步程序

1. `gh api repos/miantiao-me/Sink/commits/master --jq .sha` 取得新 commit
2. 在鎖定 commit 與新 commit 間 `git diff` 審閱（重點：wrangler 資源變動、新外連、新 env）
3. 更新本檔與 `.smallgreen/profile.yaml` 的 `upstream.commit`（兩處必須一致，CON-6 會驗）
4. 契約欄位若受影響一併更新，開 PR 走 conformance CI

## 備註

- 本 repo 不含上游程式碼；CI 依鎖定 commit 組裝後受檢（見 conformance adapter workflow）
- `patches/` 目前為空——Sink 原生即可部署於 Cloudflare，無需修補
