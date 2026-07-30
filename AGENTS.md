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
4. **建資源＋部署**（確認 2）：
   ```bash
   npx wrangler d1 create sink            # 記下 database_id
   npx wrangler kv namespace create KV    # 記下 id
   # R2 選配：帳號未啟用 R2 就跳過（省略 DEPLOY_R2_BUCKET_NAME 會自動從部署設定移除 R2）
   cat > .env <<EOF
   DEPLOY_D1_DATABASE_ID=<上面的 database_id>
   DEPLOY_D1_DATABASE_NAME=sink
   DEPLOY_KV_NAMESPACE_ID=<上面的 kv id>
   DEPLOY_ANALYTICS_DATASET=sink
   EOF
   pnpm build && pnpm deploy:worker       # deploy:config 讀 .env 產 wrangler.deploy.jsonc → migrations → deploy
   ```
   注意：Analytics Engine 需帳號先在 dashboard 一次性啟用（未啟用會 error 10089）；新帳號 workers.dev 子網域可 `PUT /accounts/:id/workers/subdomain` 註冊，TLS 佈建約 3-5 分鐘。
5. **設 secret**：產生 32+ 字元亂數後 `npx wrangler secret put NUXT_SITE_TOKEN --config wrangler.deploy.jsonc`；統計儀表板要 `NUXT_CF_ACCOUNT_ID`／`NUXT_CF_API_TOKEN`（選配，見 profile.yaml how_to_obtain）。
6. **初始化**：fresh install 也要先解 423 gate——`curl -X POST <部署URL>/api/link/migration/run -H "Authorization: Bearer <NUXT_SITE_TOKEN>"`，回 `{"completed":true,...}` 即可。
7. **驗收**（確認 3）：照 `.smallgreen/acceptance.yaml`——登入、建短鏈、開短鏈看轉址、未授權被拒。全過才算完成。

## 維護與移除

照 `.smallgreen/maintenance.yaml`。移除必須執行到資源歸零（Worker、D1、KV、R2、repo 副本全刪）。
