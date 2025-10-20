# Chapter 06：測試組織與重複利用

## 測試結構（describe, test）
- 使用 `test.describe` 組織測試群組
- 使用 `test` 撰寫個別測試案例

## beforeEach/afterEach
- 在每個測試前後執行初始化或清理
- 範例：
```typescript
test.beforeEach(async ({ page }) => {
  await page.goto('https://example.com');
});
```

## 共用函式與自訂指令
- 將重複邏輯抽成函式
- 建立自訂 helper 方便重複使用