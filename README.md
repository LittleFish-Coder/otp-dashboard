# OTP Dashboard 🔐

一個即時儀表板,自動抓取並顯示各平台寄到 Gmail 的 OTP 驗證碼。目前支援 **Surfshark VPN**,並可隨時擴充 Netflix、Booking.com、NordVPN 等其他平台。

## 運作方式

1. OTP 信件寄達 Gmail
2. 前端每 5 秒呼叫一次 Google Apps Script 的 Web App 網址
3. Apps Script 當下即時搜尋 Gmail、抓出最新的驗證碼,回傳 JSON
4. 前端解析 JSON,依後端回傳的 provider 動態渲染卡片

> 舊版是透過 GitHub Gist 中轉(Apps Script 定時寫入 Gist、前端再讀 Gist),現已改為 **Apps Script 直接回傳 JSON**,少一層中轉、沒有 CDN 快取延遲,也不會撞到 GitHub API 的速率限制。

## 架構

```
Gmail ──搜尋──> Apps Script (doGet) ──JSON──> index.html (GitHub Pages)
```

- **後端**:單一 Apps Script 檔案,`PROVIDERS` 陣列集中管理所有平台
- **前端**:單一 `index.html`,卡片依後端回傳內容動態產生

## 支援平台

| 平台 | 狀態 |
|------|------|
| Surfshark VPN | ✅ 已啟用 |
| Netflix | ⏸ 已寫好,待解開設定 |
| Booking.com | ⏸ 已寫好,待解開設定 |
| NordVPN | ⏸ 已寫好,待解開設定 |

## 新增一個平台

只需要改**一個地方**——在 Apps Script 的 `PROVIDERS` 陣列加一行,再重新部署即可,前端完全不用動:

```javascript
{ id: 'spotify', label: 'Spotify', from: 'no-reply@spotify.com', digits: 6 },
```

- `from`:該平台 OTP 信的真實寄件人地址(請打開信件確認)
- `digits`:驗證碼位數(多數為 6,Netflix 常為 4)

## 設定

**後端(Apps Script)**

1. 於 [script.google.com](https://script.google.com) 新增專案,貼上後端程式碼
2. 設定第一行的 `KEY`(建議純英數亂碼)
3. 先手動執行一次 `doGet` 以完成 Gmail 授權
4. 部署 → 網頁應用程式,執行身分「我」、存取權「任何人」
5. 取得 `.../exec` 網址

**前端(index.html)**

在 `<script>` 開頭填入兩項設定:

```javascript
const ENDPOINT = 'https://script.google.com/macros/s/AKfycb.../exec';
const KEY = '你的 key';
```

## 安全性注意

- Web App 網址加上 `?key=` 等同一把鑰匙,任何取得完整網址的人都能即時讀取你的 OTP,請只私下分享給信任的人。
- 若要把 `KEY` 藏起來、並解決前端直連可能遇到的 CORS 問題,建議加一層 Cloudflare Workers 作為代理,將 `KEY` 存於 Worker 環境變數,前端只呼叫 Worker 網址。