# Chapter 05：斷言與驗證

## 斷言語法介紹
- 使用 `expect` 進行各種驗證
- 範例：
```typescript
await expect(page).toHaveTitle('標題')
await expect(page.locator('h1')).toHaveText('歡迎')
```

## 驗證文字、屬性、狀態
- 驗證元素是否存在、是否可見
- 驗證屬性值、按鈕是否可點擊