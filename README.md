# dbt Coverage Analyzer – GitHub Pages

Snowflake Marketplace Profile 用のランディングページです。

## ディレクトリ構成

```
dbtCoveragePages/
├── index.html           # メインページ
├── assets/
│   └── css/
│       └── style.css    # スタイルシート
└── README.md
```

## GitHub Pages の公開手順

1. このディレクトリを GitHub リポジトリとして作成（例: `dbt-coverage-pages`）
2. Settings → Pages → Source: `Deploy from branch` → `main` / `/ (root)` を選択
3. 公開 URL（例: `https://your-org.github.io/dbt-coverage-pages/`）を Snowflake Marketplace の Provider Profile の Website URL に設定

## カスタマイズ

`index.html` の以下の箇所を実際の情報に更新してください：

- `https://app.snowflake.com/marketplace` → Marketplace のリスティング URL
- `https://github.com/your-org/dbtSQLBranchCoverageAnalyzer` → 実際の GitHub URL
- `© 2024` → 実際の年度・組織名
