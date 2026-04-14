# 2026database — Claude基本功 EP09 + EP09.5 實作專案

## 專案簡介
Claude基本功 EP09 與 EP09.5 的實作工作目錄：
- **EP09**：Supabase 資料庫懶人包，示範班級成績記錄本
- **EP09.5**：Firebase 串接免費資料庫，示範即時文字雲

從建資料庫、做前端、到上線，全程對話完成。

## 關鍵時程
- 影片規劃建立：2026-04-04
- 專案初始化：2026-04-12
- EP09 第二台電腦彩排完成：2026-04-12
- EP09.5 Firebase 彩排完成：2026-04-14
- **EP09 正式錄影 + 發布：2026-04-13** → https://youtu.be/aRpZL-CyS7k

## 語言與風格
- 所有回應、文件皆使用**繁體中文**
- 修改前先確認計畫，優先保留原有資料結構

## Obsidian 關聯資料
以下 Obsidian 筆記可作為佐證素材，路徑相對於 vault 根目錄：
- `創作庫/Claude基本功EP09 - Supabase資料庫懶人包.md` — EP09 腳本（已發布）
- `創作庫/Claude基本功EP09.5 - Firebase串接免費資料庫.md` — EP09.5 腳本
- `創作庫/Claude基本功EP11 - 本地AI與免費API懶人包.md` — EP11 腳本（含 Groq + Vision）
- `Claude Code 懶人包/04-連接 Supabase 資料庫.md` — Supabase 懶人包 v0.4
- `Claude Code 懶人包/05-連接 Firebase 資料庫.md` — Firebase 懶人包 v0.7

## Supabase 專案資訊

### my-teaching-tools（成績記錄本）
- 專案 ID：`xxbjykdheracbfmwpxwm`
- Region：`ap-northeast-2`
- 資料表：`students`（座號、國英數、總分、平均）
- 去識別化：統一使用「座號」，不存學生姓名
- 網頁：https://mathruffian-dot.github.io/2026database/

### teacherstudy（教學互動工具）
- 專案 ID：`pwfhkdkkobzkmsbmzldj`
- Region：`ap-southeast-2`
- 資料表：`wordcloud_words`（word, created_at）
- RLS：anon 可讀取 + 可新增，老師用 MCP 管理刪除
- 網頁：https://mathruffian-dot.github.io/2026database/wordcloud.html

## Firebase 專案資訊

### my-teaching-tools（Firebase 文字雲，EP09.5）
- 專案 ID：`my-teaching-tools`
- Region：`asia-east1` (Taiwan)
- 集合：`wordcloud_words`（word, created_at）
- Security Rules：白名單模式，wordcloud_words 公開可讀寫，其他禁止
- Web App：`1:257256401647:web:9db61fe50e7f22274cc91a`
- 網頁：https://mathruffian-dot.github.io/2026database/wordcloud-firebase.html

## 架構決策（彩排後確定）

### 三種頁面模式
| 頁面 | 功能 | 權限 | 需要登入 |
|------|------|------|---------|
| 學生提交頁 | 交作業、答題、輸入文字 | 只能寫入 | 不需要 |
| 公開展示頁 | 排行榜、文字雲、統計圖 | 只能讀取統計 | 不需要 |
| 老師管理 | Claude + MCP 直接查詢分析 | 完整權限 | 不需要（本機操作） |

### 關鍵決策
- **不使用 Google 登入**：對一般老師太複雜，改由 Claude + MCP 直接管理資料
- **去識別化**：資料庫只存座號，不存學生真名，避免個資經過 AI 服務
- **RLS 依表設定**：不同資料表可設不同權限（學生只能寫、展示頁只能讀統計）

## 平台分工指引（Supabase vs Firebase）

**🔥 使用者決定（2026-04-14）：真實使用以 Firebase 為主**

> 當使用者要做新的資料庫工具時，預設用 Firebase。除非是複雜 SQL 統計需求才考慮 Supabase。
> 已有的 Supabase 專案（成績本、文字雲）保留，不主動遷移。

### Firebase（推薦：絕大多數老師的預設選擇）
- ✅ 班級教學工具（成績、回饋、互動）
- ✅ 大型研習 IRS（並發 100 萬，Spark 免費版）
- ✅ 即時文字雲、投票、舉手
- ✅ `onSnapshot` 即時更新更簡單
- ✅ **永遠不會閒置暫停**（不用設排程）
- ✅ **無限免費專案**（Spark 方案）
- ✅ **Firebase MCP 完整支援 Firestore CRUD**（list、query、add、update、delete、auth、messaging、storage 都有）
- ✅ Claude 用自然語言直接操作資料，跟 Supabase MCP 體驗一致

### Supabase（適合：重度資料分析需求）
- ✅ 複雜 SQL 統計（JOIN、GROUP BY、跨表分析）一行搞定
- ✅ 學期累積成績匯總、跨單元交叉分析
- ⚠️ 免費版並發連線上限 200 → 不適合大型研習
- ⚠️ 免費版只能建 2 個專案
- ⚠️ 一週閒置會自動暫停（要設排程）

### 場景對照表

| 場景 | 規模 | 推薦 | 原因 |
|------|------|------|------|
| 第一次接觸資料庫的老師 | — | **Firebase** | 限制少、不會暫停、規模彈性大 |
| 班級成績記錄本 | 30 人 | **Firebase** | 不會暫停，老師也能用 Claude 查 |
| 課堂 IRS、即時互動 | 30 人 | **Firebase** | onSnapshot 即時更新最簡單 |
| 即時文字雲、投票牆 | 不限 | **Firebase** | onSnapshot 即時更新 |
| **大型研習 IRS** | **1000 人** | **Firebase**（必選） | 並發 100 萬 vs Supabase 200 |
| 教學駕駛艙 + 差異化派題 | 30 人 | Supabase | 複雜 SQL 統計查詢 |
| 學期成績匯總、跨單元分析 | — | Supabase | SQL JOIN/GROUP BY 強 |
| 已經用 Supabase 的人 | — | 繼續用 | 沒必要換 |

### 為什麼從「Supabase 為主」改為「Firebase 為主」？

| 之前的疑慮 | 2026-04-14 重新驗證 |
|-----------|---------|
| 「Firebase 不能用 Claude 查資料」 | ❌ 完全錯誤，Firebase MCP 有完整 Firestore CRUD 工具 |
| 「Firebase MCP 工具少」 | ❌ 錯，有 list/query/add/update/delete + auth + messaging + storage |
| 「Supabase MCP 體驗更直接」 | 🟡 SQL 語法稍簡潔，但 Firebase MCP 同樣能用自然語言完成 |
| 「必須透過 REST API + curl 查 Firestore」 | ❌ 不用，MCP 直接查 |

## 目前進度
- [x] 專案初始化
- [x] 連接 Supabase MCP（PAT 方式）
- [x] 建立班級成績記錄資料庫
- [x] 製作成績管理前端網頁
- [x] 加入 Google 帳號登入 + RLS 保護（彩排測試，正式版改為選配）
- [x] 部署到 GitHub Pages（公開 repo）
- [x] 設定自動防暫停排程（每週一 9:00）
- [x] 架構決策：老師端用 Claude + MCP、去識別化用座號
- [x] 文字雲互動網頁（wordcloud.html + teacherstudy 專案）
- [x] EP09.5：Firebase MCP 連接（CLI login 方式）
- [x] EP09.5：Firebase 文字雲實作（wordcloud-firebase.html）
- [x] EP09.5：Firestore 永久安全規則（白名單模式）
- [x] EP09.5：懶人包 v0.3 + 腳本完成
- [x] **EP09 正式錄影 + 上架（2026-04-13）**：https://youtu.be/aRpZL-CyS7k
- [ ] EP09.5 正式錄影 + 上架

## 最近更動紀錄
| 日期 | 變更摘要 | GDrive | Obsidian | GitHub |
|------|----------|--------|----------|--------|
| 2026-04-12 | 專案初始化 | ✅ | ✅ | ✅ |
| 2026-04-12 | 完成班級成績記錄本（含 Google 登入） | ✅ | ✅ | ✅ |
| 2026-04-12 | 架構決策：去識別化+座號、老師端用 MCP、Google 登入改選配 | ✅ | ✅ | 🔄 |
| 2026-04-13 | 新增文字雲互動網頁（teacherstudy 專案） | ✅ | ✅ | ✅ |
| 2026-04-13 | 更新懶人包 v0.4→v0.5（文字雲實戰範例） | — | ✅ | — |
| 2026-04-14 | EP09.5 Firebase 彩排：建專案、連 MCP、文字雲、安全規則 | ✅ | ✅ | ✅ |
| 2026-04-14 | EP09.5 腳本與 Firebase 懶人包 v0.3 完成 | — | ✅ | — |
| 2026-04-13 | **EP09 正式錄影 + 上架 YouTube** | — | ✅ | — |

## 資料夾結構
```
2026database/
├── CLAUDE.md                   # 專案說明
├── .gitignore                  # Git 忽略規則
├── firebase.json               # Firebase CLI 設定（EP09.5）
├── firestore.rules             # Firestore 安全規則（EP09.5）
├── .firebaserc                 # Firebase 專案設定（EP09.5）
│
├── index.html                  # 班級成績記錄本（EP09，Supabase）
├── wordcloud.html              # 文字雲互動網頁（EP09，Supabase）
├── wordcloud-firebase.html     # 文字雲互動網頁（EP09.5，Firebase）
├── math-homework.html          # 數學作業 AI 批改（EP11，Groq Vision + Firebase）
│
└── .claude/
    └── launch.json             # 本地預覽伺服器設定
```

## 檔案對應集數

| 集數 | 主題 | 示範檔案 |
|------|------|---------|
| EP09 | Supabase 資料庫懶人包 | `index.html`、`wordcloud.html` |
| EP09.5 | Firebase 串接免費資料庫 | `wordcloud-firebase.html`、`firestore.rules`、`firebase.json` |
| EP11 | 本地 AI 與免費 API（Ollama/Groq/Gemini） | `math-homework.html` |

## 彩排筆記（懶人包更新用）

以下是這次彩排發現需要更新懶人包的地方：

1. **MCP 安裝方式改變**：不再用 `--supabase-url` + `--supabase-service-role-key`，改用 Personal Access Token（`--access-token`）
2. **Supabase API Keys 介面改版**：不再叫 `anon key` / `service_role key`，改成 `Publishable key` / `Secret key`
3. **需要額外步驟**：到 Account → Tokens 產生 PAT
4. **GitHub Pages 需要公開 repo**：免費方案不支援私有 repo
5. **Google Auth 改為選配**：老師端改用 Claude + MCP 直接查資料，不需要登入機制
6. **RLS 可以每張表各自設定**：不同表可以有不同權限規則
7. **supabase-js 變數名不能叫 `supabase`**：會跟 `window.supabase` 衝突，要改名（如 `db`）

## EP09.5 Firebase 彩排重點

1. **Firebase MCP 用途不同於 Supabase MCP**：Firebase MCP 主要做專案管理（建 app、查設定、安全規則），**無法直接讀寫 Firestore 資料**
2. **登入方式**：Firebase CLI 必須在互動式終端執行 `firebase login`，無法在 Claude Code 對話中完成，要請使用者打開 cmd
3. **Firebase Config 全部可以公開**：apiKey、projectId 設計給前端使用，不算敏感資訊
4. **Firestore 規則建議白名單模式**：建立資料庫時選「正式版模式」而非「測試模式」（避免 30 天過期），立即設定白名單規則
5. **`onSnapshot` 是 Firebase 的殺手鐧**：即時更新比 Supabase Realtime 設定更簡單
6. **老師查資料怎麼辦**：Firebase MCP 無法直接查 Firestore，需透過網頁前端或 Firebase Console 操作（這點 Supabase 比較方便）

## 三處同步指引

| 平台 | 路徑 / 位置 | 用途 |
|------|-------------|------|
| Google Drive | `G:\我的雲端硬碟\2026database\` | 主要工作目錄，Claude Code 直接讀寫 |
| Obsidian | `2026database/` | 第二大腦，佐證素材與草稿撰寫 |
| GitHub | `mathruffian-dot/2026database` | 版本控制與備份（公開 repo） |

## 工作注意事項
- 此資料夾位於 Google 雲端硬碟
- 跨裝置作業，每次開始前應先瀏覽現有檔案確認最新狀態
- 新增或修改檔案後，更新「資料夾結構」與「最近更動紀錄」
- 每次對話結束前，確認三處同步狀態是否一致
- 示範時使用假資料，不要放真實學生個資
- 正式使用時統一用座號，不存學生真名（去識別化）

## 跨裝置工作流程
1. **開工前**：瀏覽「目前進度」與「最近更動紀錄」
2. **工作中**：優先在 Google Drive 編輯，完成後同步至 Obsidian
3. **收工前**：更新「最近更動紀錄」，標記同步狀態
4. **跨電腦切換**：Google Drive 自動同步、Obsidian 確認 vault 同步、GitHub `git pull`
