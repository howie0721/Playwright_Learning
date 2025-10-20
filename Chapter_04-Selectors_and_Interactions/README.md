# Chapter 04：元素選取與互動

## 使用 Selector
- Playwright 支援 CSS、text、XPath 等多種選取器
- 範例：`await page.click('text=登入')`

## 等待元素出現
- 使用 `await page.waitForSelector('selector')` 等待元素

## 表單操作
- 輸入：`await page.fill('input[name="email"]', 'test@example.com')`
- 勾選：`await page.check('input[type="checkbox"]')`
- 下拉選單：`await page.selectOption('select', 'value')`