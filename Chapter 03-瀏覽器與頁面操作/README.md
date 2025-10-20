# Chapter 03：瀏覽器與頁面操作

## 啟動瀏覽器
- 使用 Playwright API 啟動 Chromium、Firefox 或 WebKit
- 範例：
```typescript
const { chromium } = require('playwright');
const browser = await chromium.launch();
```

## 開啟新分頁
- 使用 `browser.newPage()` 建立分頁
- 範例：
```typescript
const page = await browser.newPage();
```

## 基本網頁操作
- 點擊元素：`await page.click('selector')`
- 輸入文字：`await page.fill('selector', '內容')`
- 截圖：`await page.screenshot({ path: 'screenshot.png' })`