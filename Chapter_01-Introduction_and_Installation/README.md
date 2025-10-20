# Chapter 01：Playwright 簡介與安裝

## 什麼是 Playwright？
Playwright 是由 Microsoft 開發的自動化測試框架，支援多瀏覽器（Chromium、Firefox、WebKit），可用於端到端測試、爬蟲、UI 驗證等。

## 支援的瀏覽器與平台
- Chromium（Chrome、Edge）
- Firefox
- WebKit（Safari）
- Windows、macOS、Linux

## 安裝 Playwright
1. 開啟終端機，進入專案資料夾
2. 執行 `npm init playwright@latest` 或 `npm install -D playwright`
3. 依指示安裝所需瀏覽器

### 安裝注意事項
- 建議使用 Node.js 16 以上版本
- 若遇到安裝失敗，可嘗試加上 `--force` 或檢查網路連線
- 安裝後可用 `npx playwright install` 重新安裝瀏覽器

## 建立帳號與登入
Playwright 不需註冊帳號即可使用。若需 CI/CD 或雲端服務，可整合 GitHub Actions、Azure 等平台。
