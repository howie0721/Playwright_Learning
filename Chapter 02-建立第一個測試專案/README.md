# Chapter 02：建立第一個測試專案

## 初始化專案
1. 開啟終端機，進入專案資料夾
2. 執行 `npm init playwright@latest` 初始化 Playwright 專案
3. 依指示選擇語言（TypeScript/JavaScript）與測試框架

## 建立基本測試檔案
- 在 `tests` 資料夾中新增測試檔案，如 `example.spec.ts`
- 撰寫第一個測試案例：

```typescript
import { test, expect } from '@playwright/test';

test('首頁標題驗證', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page).toHaveTitle(/Example Domain/);
});
```

## 執行測試
- 在終端機執行 `npx playwright test` 開始測試
- 查看測試結果與報告