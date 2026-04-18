# RoughStep — GitHub Pages

`roughstep.dev` で公開するポータルサイトです。

## ディレクトリ構成

```
dbtCoveragePages/
├── index.html                        # トップページ（RoughStep ポータル）
├── products/
│   └── dbt-coverage/
│       └── index.html                # CoverageAnalyst for dbt 製品紹介
├── privacy/
│   └── index.html                    # プライバシーポリシー（HTML）
├── assets/
│   └── css/
│       └── style.css                 # 製品ページ用スタイルシート
│   └── img/
│       └── roughstep-logo.svg        # ← ロゴ画像をここに配置（要差し替え）
├── CNAME                             # カスタムドメイン: roughstep.dev
└── README.md
```

## カスタムドメイン設定

`CNAME` ファイルに `roughstep.dev` を記載済みです。
DNS レジストラ側で以下の設定を行ってください。

| タイプ | ホスト名 | 値 |
|--------|----------|----|
| A      | @        | 185.199.108.153 |
| A      | @        | 185.199.109.153 |
| A      | @        | 185.199.110.153 |
| A      | @        | 185.199.111.153 |
| CNAME  | www      | roughstep.github.io |

その後、GitHub リポジトリの Settings > Pages > Custom domain に `roughstep.dev` を入力してください。
