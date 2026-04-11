---
layout: default
title: Privacy Policy — CoverageAnalyst for dbt
permalink: /docs/privacy/
---

# Privacy Policy

**CoverageAnalyst for dbt**  
最終更新日: 2026-04-11

---

## 概要 / Overview

CoverageAnalyst for dbt は、Snowflake Native App として動作します。  
本アプリはお客様の **Snowflake アカウント内でのみ** 動作し、外部サーバーへのデータ送信は一切行いません。

> This app runs entirely within your Snowflake account. No data is transmitted to external servers or third parties.

---

## 収集・処理するデータ / Data Collected and Processed

本アプリが処理するデータは以下のとおりです。

| データ | 用途 | 保存場所 |
|--------|------|----------|
| dbt アーティファクト (`manifest.json`, `run_results.json`) | カバレッジ分析の入力として使用 | お客様の Snowflake Stage |
| 分析結果 (カバレッジ率、分岐情報など) | レポート生成・履歴表示 | お客様の Snowflake アカウント内テーブル |
| ストアドプロシージャ呼び出しログ | Snowflake 標準の Query History に記録 | お客様の Snowflake アカウント |

**本アプリは以下を行いません：**

- 外部サービスへのデータ送信
- 個人情報の収集
- お客様の Snowflake アカウント外へのデータ転送
- クッキーやトラッキング技術の使用

---

## データの管理 / Data Control

お客様は Snowflake の標準機能を通じて、本アプリが保存するすべてのデータを管理・削除できます。

- **分析履歴の削除**: アプリをアンインストールすることですべてのデータが削除されます
- **アクセス制御**: Snowflake の RBAC（ロールベースアクセス制御）によって管理されます

---

## サードパーティ / Third Parties

本アプリはサードパーティのサービスやライブラリにデータを送信しません。  
アプリ内で使用している OSS ライブラリ（`sqlglot` 等）はお客様の Snowflake 環境内で動作します。

