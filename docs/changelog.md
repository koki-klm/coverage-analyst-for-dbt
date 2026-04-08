---
layout: default
title: Changelog — dbt SQL Branch Coverage Analyzer
permalink: /docs/changelog/
---

# Changelog

このプロジェクトの変更履歴です。  
フォーマットは [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) に準拠しています。

---

## [Unreleased]

---

## [0.1.0-beta.1] - 2025-07-01

### 🎉 Initial Beta Release

dbt SQL Branch Coverage Analyzer の初回ベータ版です。  
Snowflake Native App として Marketplace での公開を予定しています。

### Added

#### Core Engine
- SQL の C1（分岐網羅）カバレッジ分析機能
- `sqlglot` ベースの SQL パーサーによる分岐抽出（CASE WHEN / IFF / WHERE 句など）
- dbt `manifest.json` + `run_results.json` を入力とした自動解析
- 複数 YAML ファイルへのテストケース分散に対応

#### Snowflake Native App
- Snowflake ステージ上の `target.zip` を直接解析する `analyze_coverage` ストアドプロシージャ
- 実行履歴を取得する `list_history` ストアドプロシージャ
- HTML レポートを生成する `generate_html_report` ストアドプロシージャ
- Streamlit UI からのインタラクティブなカバレッジ確認
- CI/CD から直接 SP を呼び出せる snowsql 対応（`-o output_format=json`）
- 分析履歴の記録（`analysis_history` テーブル）

#### i18n（多言語対応）
- 日本語（ja）・英語（en）の UI テキスト対応
- `lang` パラメータによる言語切り替え

### Known Limitations (Beta)

- Snowflake 以外のデータウェアハウスは未対応
- `MERGE` 文のブランチ分析は未対応

---

[Unreleased]: https://github.com/your-org/dbtCoveragePages/compare/v0.1.0-beta.1...HEAD
[0.1.0-beta.1]: https://github.com/your-org/dbtCoveragePages/releases/tag/v0.1.0-beta.1
