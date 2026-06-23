# skills

[Google Engineering Practices](https://google.github.io/eng-practices/) に準拠した Claude Code 用スキル集。

このリポジトリは Claude Code の **プラグイン・マーケットプレイス**として構成されており、コマンド一発でインストールできます。

## インストール

Claude Code 上で次を実行します。

```text
/plugin marketplace add mr04vv/skills
/plugin install eng-practices@mr04vv-skills
/reload-plugins
```

- 1 行目でこのリポジトリをマーケットプレイスとして登録します（初回のみ）。
- 2 行目で `eng-practices` プラグイン（3 スキル + 正典）をインストールします。
- 別のマシンでも、同じ 2 コマンドで導入できます。

更新・管理:

```text
/plugin marketplace update mr04vv-skills    # マーケットプレイス定義の更新
/plugin list                                 # インストール済み一覧
/plugin uninstall eng-practices@mr04vv-skills
```

インストール後、各スキルは **プラグイン名前空間付き**で呼び出せます（例: `/eng-practices:eng-review`）。説明文のトリガー語（「Google 流レビューして」「要件を分解して」「PR 概要を作って」等）でも起動します。

## 収録プラグインとスキル

### `eng-practices` プラグイン

| スキル | 役割 | 準拠する原文ページ |
| --- | --- | --- |
| `eng-review` | git diff / ブランチ差分 / コミットを Design・Functionality・Complexity・Tests・Naming・Comments・Style・Documentation の 8 観点でレビューし、礼儀正しく重要度ラベル（Nit / Optional / FYI）付きのレビューコメント文面まで生成する | The Standard of Code Review / What to look for / Navigating a CL / How to write code review comments / Speed |
| `eng-split` | 要件や大きな作業を「one self-contained change」に分解。水平分割（レイヤー単位）・垂直分割（機能単位）・リファクタリングの分離・ビルドを壊さないマージ順序を含む分割計画を出力する | Small CLs |
| `eng-pr` | 変更差分から CL 説明文 / PR タイトル・本文を生成する。1 行目は命令文の自己完結した要約、本文は why と文脈（問題・選んだ理由・欠点・背景情報） | Writing good CL descriptions |

すべてのスキルは、同梱の正典 `plugins/eng-practices/reference/google-eng-practices-verbatim.md` を `${CLAUDE_PLUGIN_ROOT}` 経由で参照し、記憶や一般論ではなく**原文の記述を判断根拠**とします。

## リポジトリ構成

```text
.
├── .claude-plugin/
│   └── marketplace.json              # マーケットプレイス定義（mr04vv-skills）
├── plugins/
│   └── eng-practices/
│       ├── .claude-plugin/
│       │   └── plugin.json           # プラグイン定義
│       ├── skills/
│       │   ├── eng-review/SKILL.md
│       │   ├── eng-split/SKILL.md
│       │   └── eng-pr/SKILL.md
│       └── reference/
│           └── google-eng-practices-verbatim.md   # 正典（原文の書き起こし）
└── README.md
```

## 正典について

`plugins/eng-practices/reference/google-eng-practices-verbatim.md` は、<https://google.github.io/eng-practices/> の内容を、要約・改変を加えず元の Markdown ソース（<https://github.com/google/eng-practices>）からそのまま書き起こしたものです。

## 実行範囲

- `eng-review` / `eng-pr` は **`git diff` の読み取りまで**を行い、commit / push / PR 作成は行いません（`eng-pr` のみ、ユーザー確認後に任意で実行を提案）。
- `eng-split` は **分割計画の作成まで**を行い、実装やブランチ作成は行いません。

## ライセンス

`reference/` 内の文書は Google Engineering Practices の原文であり、[CC-BY 3.0 License](https://creativecommons.org/licenses/by/3.0/) の下にあります。
