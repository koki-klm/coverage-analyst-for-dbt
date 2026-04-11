---
layout: default
title: API Reference — dbt SQL Branch Coverage Analyzer
permalink: /docs/api-reference/
---

# Stored Procedure API Reference

dbt SQL Branch Coverage Analyzer が提供するストアドプロシージャの仕様です。  
CI/CD パイプラインや独自スクリプトから呼び出す際の参考にしてください。

> 🇺🇸 An English summary is provided for each section below.

---

## Table of Contents

1. [analyze_coverage](#1-analyze_coverage)
2. [list_history](#2-list_history)
3. [generate_html_report](#3-generate_html_report)
4. [Calling from snowsql](#4-calling-from-snowsql)
5. [Error Response Format](#5-error-response-format)

---

## 1. analyze_coverage

dbt `target.zip` を読み込み、SQL 分岐カバレッジ（C1カバレッジ）を測定します。

### Signature

```sql
CALL code.analyze_coverage(
  stage_path     STRING,   -- Stageパス（例: '@dbt_coverage_app.code.dbt_artifacts/target.zip'）
  model_selector STRING,   -- モデルセレクター（'*' で全モデル、'fact_sales' で特定モデル）
  lang           STRING    -- メッセージ言語: 'ja' または 'en'（省略時: 'en'）
);
```

### Response JSON Schema

```json
{
  "status": "success",
  "run_id": "abc123-...",
  "timestamp": "2026-04-08T12:34:56Z",
  "stage_path": "@dbt_coverage_app.code.dbt_artifacts/target.zip",
  "model_selector": "*",
  "summary": {
    "analyzed_models": 6,
    "total_branches": 112,
    "covered_branches": 105,
    "coverage_rate_percent": 93.75,
    "models_passed": 5,
    "models_failed": 1
  },
  "models": [
    {
      "name": "fact_sales",
      "status": "PASS",
      "coverage": 100.0,
      "total_branches": 5,
      "covered_branches": 5,
      "uncovered_branches": 0,
      "uncovered_line_numbers": []
    },
    {
      "name": "service_revenue_analysis",
      "status": "FAIL",
      "coverage": 66.7,
      "total_branches": 3,
      "covered_branches": 2,
      "uncovered_branches": 1,
      "uncovered_line_numbers": [
        {
          "line_number": 88,
          "condition": "status = 'active'"
        }
      ]
    }
  ]
}
```

### status values

| status | Description |
|--------|-------------|
| `"success"` | All models analyzed successfully |
| `"partial"` | Some models failed, others completed |
| `"error"` | Analysis failed entirely (auth error, ZIP not found, etc.) |

### Example

```sql
USE APPLICATION dbt_coverage_app;
CALL code.analyze_coverage(
  '@dbt_coverage_app.code.dbt_artifacts/target.zip',
  '*',
  'en'
);
```

---

## 2. list_history

実行履歴を取得します。

### Signature

```sql
CALL code.list_history(
  limit_count INT   -- 取得件数（デフォルト: 20）
);
```

### Response JSON Schema

```json
{
  "status": "success",
  "history": [
    {
      "RUN_ID": "abc123-...",
      "EXECUTED_AT": "2026-04-08 12:34:56",
      "STAGE_PATH": "@dbt_coverage_app.code.dbt_artifacts/target.zip",
      "MODEL_SELECTOR": "*",
      "STATUS": "success",
      "TOTAL_MODELS": 6,
      "ANALYZED_MODELS": 6,
      "TOTAL_BRANCHES": 112,
      "COVERED_BRANCHES": 105,
      "COVERAGE_RATE": 93.75
    }
  ]
}
```

### Example

```sql
USE APPLICATION dbt_coverage_app;
CALL code.list_history(10);
```

---

## 3. generate_html_report

`analyze_coverage` の結果 JSON から HTML レポートを生成します。  
CI/CD パイプラインで HTML レポートをアーティファクトとして保存したい場合に使用します。

### Signature

```sql
CALL code.generate_html_report(
  result_json STRING,   -- analyze_coverage が返した JSON 文字列
  lang        STRING    -- レポート言語: 'ja' または 'en'（デフォルト: 'en'）
);
```

### Response

**Success**: HTML string starting with `<!DOCTYPE html>`

**Error**: JSON string

```json
{
  "status": "error",
  "message": "Error details"
}
```

### Example

```sql
USE APPLICATION dbt_coverage_app;

SET result = (
  CALL code.analyze_coverage(
    '@dbt_coverage_app.code.dbt_artifacts/target.zip',
    '*', 'en'
  )
);

CALL code.generate_html_report($result, 'en');
```

---

## 4. Calling from snowsql

### Use `-o output_format=json`

`snowsql` のデフォルト出力はテーブル罫線付きのため、`-o output_format=json` を指定して JSON 形式で受け取ることを推奨します。

```bash
snowsql -c my_connection -r accountadmin \
  -o output_format=json \
  -q "USE APPLICATION dbt_coverage_app; CALL code.analyze_coverage('@dbt_coverage_app.code.dbt_artifacts/target.zip', '*', 'en');"
```

### Output format

```
[{"status": "Statement executed successfully."}]
1 Row(s) produced. Time Elapsed: 0.208s
[{"ANALYZE_COVERAGE": "{\"status\":\"success\",\"run_id\":\"...\",\"summary\":{...},\"models\":[...]}"}]
1 Row(s) produced. Time Elapsed: 2.534s
```

- **1st array**: Result of `USE APPLICATION` — can be ignored
- **2nd array**: Result of `CALL` — the SP return value is in the `ANALYZE_COVERAGE` property

### PowerShell

```powershell
$rawOutput = snowsql -c $ConnectionName -r $Role `
  -o output_format=json `
  -q "USE APPLICATION dbt_coverage_app; CALL code.analyze_coverage('$StagePath', '*', 'en');" 2>&1

$fullText = ($rawOutput -join "`n").Trim()

# Extract the last line starting with '[' (= CALL result)
$jsonLine = ($fullText -split "`r?`n") |
  Where-Object { $_.Trim().StartsWith('[') } |
  Select-Object -Last 1

$rows   = $jsonLine | ConvertFrom-Json
$result = $rows[0].PSObject.Properties | Select-Object -First 1 -ExpandProperty Value
$report = $result | ConvertFrom-Json

# Use $report.status, $report.summary, $report.models ...
```

### Python

snowflake-connector-python を使う方法：

```python
import snowflake.connector
import json

conn = snowflake.connector.connect(
    account=SNOWFLAKE_ACCOUNT,
    user=SNOWFLAKE_USER,
    password=SNOWFLAKE_PASSWORD,
    role=SNOWFLAKE_ROLE,
)
cursor = conn.cursor()

cursor.execute("USE APPLICATION dbt_coverage_app")
cursor.execute("""
    CALL code.analyze_coverage(
        '@dbt_coverage_app.code.dbt_artifacts/target.zip',
        '*', 'en'
    )
""")

row = cursor.fetchone()
result = json.loads(row[0])

print(result["status"])
print(result["summary"])
for model in result["models"]:
    print(f"{model['name']}: {model['coverage']}%")
```

---

## 5. Error Response Format

すべての SP はエラー時に以下の形式の JSON 文字列を返します：

```json
{
  "status": "error",
  "message": "Error details in the requested language"
}
```

### Common Errors

| message | 原因 | 対処法 |
|---------|------|--------|
| `Stage file open failed` | target.zip が Stage に存在しない | `LIST @dbt_coverage_app.code.dbt_artifacts` で確認 |
| `No models found` | model_selector に一致するモデルがない | `*` で全モデル選択するか、モデル名を確認 |
| `Invalid JSON` | generate_html_report に渡した JSON が不正 | analyze_coverage の結果をそのまま渡す |
| `Artifact load failed` | manifest.json または run_results.json が ZIP に含まれていない | `dbt compile` を再実行して target.zip を再作成 |

---

## Related Links

- [Changelog](changelog) — Release history
- [Snowflake Native Apps Documentation](https://docs.snowflake.com/en/developer-guide/native-apps/native-apps-about)
- [SnowSQL CLI Reference](https://docs.snowflake.com/en/user-guide/snowsql-install-config)
