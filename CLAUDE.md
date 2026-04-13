# 2026database — Claude基本功 EP09 實作專案

## 專案簡介
Claude基本功 EP09：Supabase 資料庫懶人包的實作工作目錄。
目標是示範如何用 Claude Code + Supabase 做出一個「班級成績記錄本」網頁工具，
從建資料庫、做前端、到上線，全程對話完成。

## 關鍵時程
- 影片規劃建立：2026-04-04
- 專案初始化：2026-04-12
- 第二台電腦彩排完成：2026-04-12

## 語言與風格
- 所有回應、文件皆使用**繁體中文**
- 修改前先確認計畫，優先保留原有資料結構

## Obsidian 關聯資料
以下 Obsidian 筆記可作為佐證素材，路徑相對於 vault 根目錄：
- `創作庫/Claude基本功EP09 - Supabase資料庫懶人包.md` — EP09 腳本大綱與製作進度
- `Claude Code 懶人包/04-連接 Supabase 資料庫.md` — Supabase 懶人包 v0.3

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
- [ ] 正式錄影前：移除 Google 登入、改用座號
- [ ] 更新懶人包（v0.3 → v1.0）
- [ ] 正式錄影（第一台電腦）

## 最近更動紀錄
| 日期 | 變更摘要 | GDrive | Obsidian | GitHub |
|------|----------|--------|----------|--------|
| 2026-04-12 | 專案初始化 | ✅ | ✅ | ✅ |
| 2026-04-12 | 完成班級成績記錄本（含 Google 登入） | ✅ | ✅ | ✅ |
| 2026-04-12 | 架構決策：去識別化+座號、老師端用 MCP、Google 登入改選配 | ✅ | ✅ | 🔄 |
| 2026-04-13 | 新增文字雲互動網頁（teacherstudy 專案） | ✅ | ⬜ | ⬜ |

## 資料夾結構
```
2026database/
├── CLAUDE.md              # 專案說明
├── .gitignore             # Git 忽略規則
├── index.html             # 班級成績記錄本（含 Google 登入）
├── wordcloud.html         # 文字雲互動網頁（teacherstudy 專案）
└── .claude/
    └── launch.json        # 本地預覽伺服器設定
```

## 彩排筆記（懶人包更新用）

以下是這次彩排發現需要更新懶人包的地方：

1. **MCP 安裝方式改變**：不再用 `--supabase-url` + `--supabase-service-role-key`，改用 Personal Access Token（`--access-token`）
2. **Supabase API Keys 介面改版**：不再叫 `anon key` / `service_role key`，改成 `Publishable key` / `Secret key`
3. **需要額外步驟**：到 Account → Tokens 產生 PAT
4. **GitHub Pages 需要公開 repo**：免費方案不支援私有 repo
5. **Google Auth 改為選配**：老師端改用 Claude + MCP 直接查資料，不需要登入機制
6. **RLS 可以每張表各自設定**：不同表可以有不同權限規則
7. **supabase-js 變數名不能叫 `supabase`**：會跟 `window.supabase` 衝突，要改名（如 `db`）

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
