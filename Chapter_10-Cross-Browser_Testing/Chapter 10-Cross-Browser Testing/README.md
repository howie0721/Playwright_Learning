# Chapter 10：跨瀏覽器測試

## 設定多瀏覽器執行
- 在 `playwright.config` 設定多瀏覽器
- 範例：
```typescript
projects: [
  { name: 'chromium', use: { browserName: 'chromium' } },
  { name: 'firefox', use: { browserName: 'firefox' } },
  { name: 'webkit', use: { browserName: 'webkit' } },
]
```

## 比較瀏覽器差異
- 觀察不同瀏覽器的測試結果
- 處理相容性問題