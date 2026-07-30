# AGENTS.md — Sink 部署引導（跨 CLI）

你是導遊不是裁判：每一關綠燈由機械驗證判定。判定不過就停下回報，不要繞過。同一步驟失敗 3 次即停，記錄卡點。

## 這是什麼

[Sink](https://github.com/miantiao-me/Sink)（短網址＋點擊統計）的 SmallGreen 適配 repo。上游程式碼不在這裡——先照 [UPSTREAM.md](UPSTREAM.md) 鎖定 commit 取回上游，契約在 `.smallgreen/`。

## 部署流程（使用者確認不超過 3 次）

1. **說明**：讀 `.smallgreen/profile.yaml` 向使用者說明：資料（短鏈＋點擊統計）全存自己帳號的 D1/KV/R2；外連只有三類（自己帳號的 Analytics API、使用者輸入的目標網址、選配 webhook），逐條唸給使用者聽。
2. **同意**（確認 1）後組裝：
   ```bash
   git clone https://github.com/miantiao-me/Sink.git sink && cd sink
   git checkout 3f8d30ae8c917f269249d6c7c1c3894fc8d8af91   # 與 UPSTREAM.md 一致
   corepack enable && pnpm install
   ```
3. **本地驗證**：`pnpm dev` 起本地站，開 http://localhost:3000 確認首頁。
4. **建資源＋部署**（確認 2）：照上游 README 的 Workers 部署節（`pnpm build` 後 `pnpm deploy:worker`；DEPLOY_* 環境變數帶入 D1/KV/R2 id——先 `wrangler d1 create sink`、`wrangler kv namespace create KV`、`wrangler r2 bucket create sink`）。
5. **設 secret**：`wrangler secret put NUXT_SITE_TOKEN`（必要）；統計儀表板要 `NUXT_CF_ACCOUNT_ID`／`NUXT_CF_API_TOKEN`（選配，見 profile.yaml how_to_obtain）。
6. **驗收**（確認 3）：照 `.smallgreen/acceptance.yaml`——登入、建短鏈、無痕開短鏈看轉址。全過才算完成。

## 維護與移除

照 `.smallgreen/maintenance.yaml`。移除必須執行到資源歸零（Worker、D1、KV、R2、repo 副本全刪）。
