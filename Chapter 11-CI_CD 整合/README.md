# Chapter 11：CI/CD 整合

## 在 CI 平台執行 Playwright
- 可整合 GitHub Actions、Azure Pipelines 等
- 範例 GitHub Actions 設定：
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: npm install
      - name: Run Playwright tests
        run: npx playwright test
```

## 測試自動化流程
- 讓測試自動執行於每次推送或 PR