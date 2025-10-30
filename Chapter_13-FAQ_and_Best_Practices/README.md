# Chapter 13：常見問題與最佳實踐# Chapter 13：常見問題與最佳實踐



> **學習目標**## 常見錯誤排除

> - 掌握 Playwright 最佳實踐- 測試執行失敗、元素找不到

> - 解決常見問題- 瀏覽器啟動問題

> - 優化測試效能

> - 提升測試可維護性## 測試效能優化

> - 建立專業測試團隊標準- 減少不必要的等待

- 測試資料重用

---

## 撰寫可維護測試

## 📚 本章內容- 測試命名規則

- 結構化測試檔案

## Part 1：最佳實踐- 避免硬編碼

### 1. 🎯 測試設計原則

#### ✅ AAA 模式（Arrange-Act-Assert）

```typescript
test('使用 AAA 模式', async ({ page }) => {
  // Arrange - 準備測試環境
  await page.goto('https://example.com/login');
  const username = 'testuser';
  const password = 'password123';
  
  // Act - 執行測試動作
  await page.fill('#username', username);
  await page.fill('#password', password);
  await page.click('button[type="submit"]');
  
  // Assert - 驗證結果
  await expect(page).toHaveURL(/dashboard/);
  await expect(page.locator('.welcome')).toBeVisible();
});
```

---

#### ✅ 測試獨立性

```typescript
// ❌ 錯誤：測試之間有依賴
test('建立文章', async ({ page }) => {
  // 建立文章...
});

test('編輯文章', async ({ page }) => {
  // 假設上一個測試已建立文章 ❌
});

// ✅ 正確：每個測試獨立
test('編輯文章', async ({ page }) => {
  // 1. 先建立測試資料
  await createArticle(page, 'Test Article');
  
  // 2. 執行測試
  await editArticle(page, 'Updated Article');
  
  // 3. 驗證結果
  await expect(page.locator('.article-title')).toHaveText('Updated Article');
});
```

---

#### ✅ 單一職責原則

```typescript
// ❌ 錯誤：測試太多功能
test('使用者流程', async ({ page }) => {
  await login(page);
  await createPost(page);
  await commentOnPost(page);
  await sharePost(page);
  await logout(page);
});

// ✅ 正確：拆分成多個測試
test.describe('使用者功能', () => {
  test('可以登入', async ({ page }) => {
    await login(page);
    await expect(page).toHaveURL(/dashboard/);
  });
  
  test('可以建立文章', async ({ page }) => {
    await login(page);
    await createPost(page, 'New Post');
    await expect(page.locator('.post-title')).toHaveText('New Post');
  });
  
  test('可以留言', async ({ page }) => {
    await login(page);
    await createPost(page, 'Post with Comment');
    await commentOnPost(page, 'Great post!');
    await expect(page.locator('.comment')).toContainText('Great post!');
  });
});
```

---

### 2. 🎭 Locator 最佳實踐

```typescript
// ✅ 優先順序（由高到低）
// 1. Role Locators（最推薦）
await page.getByRole('button', { name: '提交' }).click();

// 2. Test ID
await page.getByTestId('submit-button').click();

// 3. Label（表單元素）
await page.getByLabel('使用者名稱').fill('john');

// 4. Placeholder
await page.getByPlaceholder('輸入電子郵件').fill('test@example.com');

// 5. Text
await page.getByText('登入').click();

// ❌ 避免使用
// XPath（效能差）
await page.locator('xpath=//button[@id="submit"]').click();

// 過於脆弱的 CSS（容易變動）
await page.locator('div > div > div.container > button.btn-primary-large').click();
```

---

### 3. 🔍 等待策略

```typescript
// ✅ 使用 Web-first Assertions（自動等待）
await expect(page.locator('.message')).toBeVisible();
await expect(page.locator('.status')).toHaveText('完成');

// ✅ 等待特定狀態
await page.locator('.element').waitFor({ state: 'visible' });

// ✅ 等待網路請求
await page.waitForResponse(resp => 
  resp.url().includes('/api/data') && resp.status() === 200
);

// ❌ 避免使用固定延遲
await page.waitForTimeout(5000);  // 不穩定且浪費時間
```

---

### 4. 📁 專案結構

```
playwright-project/
├── tests/                          # 測試檔案
│   ├── auth/
│   │   ├── login.spec.ts
│   │   └── logout.spec.ts
│   ├── products/
│   │   ├── list.spec.ts
│   │   └── detail.spec.ts
│   └── checkout/
│       └── checkout.spec.ts
│
├── pages/                          # Page Objects
│   ├── BasePage.ts
│   ├── LoginPage.ts
│   ├── DashboardPage.ts
│   └── components/
│       ├── Header.ts
│       └── Footer.ts
│
├── fixtures/                       # 自訂 Fixtures
│   └── customFixtures.ts
│
├── utils/                          # 工具函式
│   ├── testData.ts
│   ├── helpers.ts
│   └── apiHelpers.ts
│
├── configs/                        # 配置檔案
│   ├── base.config.ts
│   ├── dev.config.ts
│   └── prod.config.ts
│
├── .env.example                    # 環境變數範例
├── playwright.config.ts            # 主配置檔
└── package.json
```

---

## Part 2：常見問題

### ❓ 問題 1：元素找不到

```typescript
// 問題
await page.locator('#button').click();
// Error: Timeout 30000ms exceeded

// 解決方案
// 1. 確認元素存在
await expect(page.locator('#button')).toBeVisible();

// 2. 等待載入完成
await page.waitForLoadState('networkidle');

// 3. 使用更穩定的選擇器
await page.getByRole('button', { name: 'Submit' }).click();

// 4. 增加超時時間
await page.locator('#slow-button').click({ timeout: 60000 });
```

---

### ❓ 問題 2：Strict Mode Violation

```typescript
// 問題
await page.locator('button').click();
// Error: strict mode violation: locator('button') resolved to 5 elements

// 解決方案
// 1. 使用更精確的選擇器
await page.locator('button:has-text("提交")').click();

// 2. 使用 first()
await page.locator('button').first().click();

// 3. 使用 nth()
await page.locator('button').nth(2).click();

// 4. 使用 filter
await page.locator('button').filter({ hasText: '提交' }).click();
```

---

### ❓ 問題 3：測試不穩定（Flaky Tests）

```typescript
// 原因與解決
// 1. 時間相關問題
// ❌ 使用固定延遲
await page.waitForTimeout(3000);

// ✅ 使用明確等待
await page.waitForSelector('.element');
await expect(page.locator('.element')).toBeVisible();

// 2. 動畫影響
// ✅ 停用動畫
await page.addStyleTag({
  content: '*, *::before, *::after { animation: none !important; transition: none !important; }',
});

// 3. 網路請求未完成
// ✅ 等待網路閒置
await page.goto('https://example.com', { waitUntil: 'networkidle' });

// 4. 競態條件
// ✅ 使用 Promise.all
await Promise.all([
  page.waitForResponse('**/api/data'),
  page.click('button#load-data'),
]);
```

---

### ❓ 問題 4：截圖/影片未生成

```typescript
// 確認配置
export default defineConfig({
  use: {
    screenshot: 'only-on-failure',    // 或 'on'
    video: 'retain-on-failure',       // 或 'on'
    trace: 'on-first-retry',          // 或 'on'
  },
});

// 手動截圖
await page.screenshot({ path: 'screenshot.png' });

// 元素截圖
await page.locator('.element').screenshot({ path: 'element.png' });
```

---

### ❓ 問題 5：CI 環境測試失敗

```typescript
// 常見原因與解決
// 1. 瀏覽器未安裝
// 解決：npx playwright install --with-deps

// 2. 權限問題
// 解決：使用官方 Docker 映像
// FROM mcr.microsoft.com/playwright:v1.49.0-jammy

// 3. 記憶體不足
// 解決：減少並行數量
export default defineConfig({
  workers: process.env.CI ? 1 : undefined,
});

// 4. 超時
// 解決：增加超時時間
export default defineConfig({
  timeout: 60000,  // 60 秒
});

// 5. 無頭模式差異
// 解決：確保 CI 使用無頭模式
export default defineConfig({
  use: {
    headless: true,
  },
});
```

---

## Part 3：效能優化

### ⚡ 1. 並行執行

```typescript
// playwright.config.ts
export default defineConfig({
  fullyParallel: true,                    // 啟用完全並行
  workers: process.env.CI ? 2 : undefined,
});

// 測試層級並行
test.describe.configure({ mode: 'parallel' });

test('測試 1', async ({ page }) => {
  // 與其他測試並行執行
});

test('測試 2', async ({ page }) => {
  // 與其他測試並行執行
});
```

---

### ⚡ 2. 重用登入狀態

```typescript
// global-setup.ts
import { chromium, FullConfig } from '@playwright/test';

async function globalSetup(config: FullConfig) {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  
  await page.goto('http://localhost:3000/login');
  await page.fill('#username', 'testuser');
  await page.fill('#password', 'password');
  await page.click('button[type="submit"]');
  
  // 保存登入狀態
  await page.context().storageState({ path: 'auth.json' });
  await browser.close();
}

export default globalSetup;

// playwright.config.ts
export default defineConfig({
  globalSetup: require.resolve('./global-setup'),
  
  use: {
    storageState: 'auth.json',  // 所有測試使用登入狀態
  },
});
```

---

### ⚡ 3. 測試分片

```bash
# 在多台機器分散執行
# 機器 1
npx playwright test --shard=1/4

# 機器 2
npx playwright test --shard=2/4

# 機器 3
npx playwright test --shard=3/4

# 機器 4
npx playwright test --shard=4/4
```

---

### ⚡ 4. 減少不必要的功能

```typescript
export default defineConfig({
  use: {
    video: 'off',              // 不錄製影片（除非需要除錯）
    screenshot: 'off',         // 不截圖（除非測試失敗）
    trace: 'on-first-retry',   // 只在重試時啟用 trace
  },
});
```

---

### ⚡ 5. Mock 外部依賴

```typescript
test('使用 Mock 加速測試', async ({ page }) => {
  // Mock API 請求
  await page.route('**/api/**', async (route) => {
    await route.fulfill({
      status: 200,
      contentType: 'application/json',
      body: JSON.stringify({ data: 'mock data' }),
    });
  });
  
  await page.goto('https://example.com');
  // 不需要等待真實 API，測試更快
});
```

---

## Part 4：團隊協作

### 📋 1. 程式碼審查檢查清單

```markdown
✅ 測試是否使用推薦的 Locator（Role > TestID）
✅ 測試是否獨立（不依賴其他測試）
✅ 是否避免使用 waitForTimeout
✅ 是否使用 Web-first Assertions
✅ 測試名稱是否清楚描述測試內容
✅ 是否遵循 AAA 模式
✅ 是否使用 Page Object Model
✅ 是否有適當的錯誤處理
✅ 是否移除 .only() 和 console.log()
```

---

### 📋 2. 測試命名規範

```typescript
// ✅ 好的命名
test('使用者可以成功登入並看到歡迎訊息', async ({ page }) => {});
test('當密碼錯誤時應顯示錯誤訊息', async ({ page }) => {});
test('購物車應顯示正確的商品數量', async ({ page }) => {});

// ❌ 不好的命名
test('test1', async ({ page }) => {});
test('登入', async ({ page }) => {});
test('check if button works', async ({ page }) => {});
```

---

### 📋 3. Git Commit 規範

```bash
# 功能
feat(auth): 新增登入測試

# 修復
fix(checkout): 修正購物車數量驗證

# 重構
refactor(pages): 將登入邏輯抽取為 Page Object

# 文件
docs(readme): 更新測試執行指南

# 測試
test(api): 新增 API 測試案例
```

---

## Part 5：安全性

### 🔒 1. 敏感資料處理

```typescript
// ❌ 不要硬編碼
const password = 'MyPassword123';  // 危險

// ✅ 使用環境變數
const password = process.env.TEST_PASSWORD;

// ✅ 使用 .env 檔案（不要提交到 Git）
// .gitignore
.env
.env.local
auth.json
```

---

### 🔒 2. 測試資料管理

```typescript
// ✅ 使用測試專用帳號
const testUser = {
  username: 'test_user_' + Date.now(),  // 動態生成
  email: `test${Date.now()}@example.com`,
  password: process.env.TEST_PASSWORD,
};

// ✅ 測試後清理資料
test.afterEach(async ({ page }) => {
  await deleteTestData(page);
});
```

---

## Part 6：監控與維護

### 📊 1. 測試報告分析

```bash
# 產生測試報告
npx playwright test --reporter=html

# 查看趨勢
# 使用 CI/CD 儲存歷史報告，分析：
# - 測試執行時間趨勢
# - 失敗率
# - 最常失敗的測試
```

---

### 📊 2. 定期維護

```markdown
每週任務：
✅ 檢查 Flaky Tests（不穩定的測試）
✅ 更新過時的選擇器
✅ 移除重複的測試
✅ 優化慢速測試

每月任務：
✅ 更新 Playwright 版本
✅ 審查測試覆蓋率
✅ 重構 Page Objects
✅ 更新測試文檔
```

---

## 參考資源

- 📖 [Best Practices - Official](https://playwright.dev/docs/best-practices)
- 📖 [Debugging Guide](https://playwright.dev/docs/debug)
- 📖 [Release Notes](https://playwright.dev/docs/release-notes)
- 💬 [Discord Community](https://aka.ms/playwright/discord)
- 🐛 [GitHub Issues](https://github.com/microsoft/playwright/issues)
- 🎓 [Playwright Training](https://learn.microsoft.com/training/modules/build-with-playwright/)

---

## 🎓 結語

恭喜你完成 Playwright 完整學習！

### 你已經掌握：
- ✅ Playwright 核心概念與架構
- ✅ 測試撰寫與組織
- ✅ Page Object Model 設計模式
- ✅ 跨瀏覽器與裝置測試
- ✅ CI/CD 整合
- ✅ 除錯與效能優化
- ✅ 最佳實踐與團隊協作

### 下一步建議：
1. 🚀 **實作專案** - 為現有專案建立測試套件
2. 📚 **深入研究** - 探索 Playwright 進階功能
3. 🤝 **參與社群** - 加入 Discord 社群交流
4. 📝 **分享經驗** - 撰寫部落格文章或教學
5. 🎯 **持續學習** - 關注 Playwright 最新發展

---

**Keep Testing! 🎭**

**Repository**: [Playwright_Learning](https://github.com/howie0721/Playwright_Learning)
