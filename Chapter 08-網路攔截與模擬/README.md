# Chapter 08：網路攔截與模擬

## 攔截 API 請求
- 使用 `page.route` 攔截並修改 API 請求

## 模擬回應資料
- 回傳自訂資料給前端
- 範例：
```typescript
await page.route('**/api/**', route => {
  route.fulfill({ status: 200, body: JSON.stringify({ success: true }) });
});
```

## 網路條件模擬
- 模擬離線、慢速網路
- 範例：`await page.emulateNetworkConditions({ offline: true })`