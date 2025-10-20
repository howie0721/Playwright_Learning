# Chapter 07：進階瀏覽器控制

## 多分頁與多視窗
- 使用 `browser.newPage()` 建立多分頁
- 控制多個分頁間的互動

## iframe 操作
- 使用 `frameLocator` 進行 iframe 內部操作
- 範例：`await page.frameLocator('iframe').locator('button').click()`

## 檔案上傳/下載
- 上傳：`await page.setInputFiles('input[type="file"]', '檔案路徑')`
- 下載：設定下載路徑並監聽下載事件