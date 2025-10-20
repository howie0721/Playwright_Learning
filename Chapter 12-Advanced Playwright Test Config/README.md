# Chapter 12：Playwright Test 進階設定

## 配置檔案（playwright.config）
- 設定 baseURL、timeout、瀏覽器選項等
- 範例：
```typescript
module.exports = {
  timeout: 30000,
  use: { baseURL: 'https://example.com' }
};
```

## 環境變數與參數化
- 使用 process.env 取得環境變數
- 參數化測試資料