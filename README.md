# skills

[Google Engineering Practices](https://google.github.io/eng-practices/) に準拠した Claude Code 用スキル集。

[Agent Skills 仕様](https://agentskills.io/specification)（`skills/*/SKILL.md`）に従って構成されており、GitHub CLI の `gh skill install` でインストールできます。

## インストール

GitHub CLI（`gh` 2.90.0 以降、`gh skill` は preview 機能）で、Claude Code 用にインストールします。

```sh
# ユーザースコープ（ホーム配下 ~/.claude/skills/ に入り、全プロジェクトで使える）
gh skill install mr04vv/skills eng-review --agent claude-code --scope user
gh skill install mr04vv/skills eng-split  --agent claude-code --scope user
gh skill install mr04vv/skills eng-pr     --agent claude-code --scope user
```

プロジェクト単位で入れたい場合は、対象リポジトリ内で `--scope project`（`.claude/skills/` に入る）を指定します。

```sh
gh skill install mr04vv/skills eng-review --agent claude-code --scope project
```

その他の操作:

```sh
gh skill preview mr04vv/skills eng-review   # 中身を確認（インストールしない）
gh skill update                             # インストール済みスキルを最新へ更新
```

> インストール後、各スキルは名前（`/eng-review` 等）か、説明文のトリガー語（「Google 流レビューして」「要件を分解して」「PR 概要を作って」等）で起動します。

## 収録スキル

| スキル | 役割 | 準拠する原文ページ |
| --- | --- | --- |
| `eng-review` | git diff / ブランチ差分 / コミットを Design・Functionality・Complexity・Tests・Naming・Comments・Style・Documentation の 8 観点でレビューし、礼儀正しく重要度ラベル（Nit / Optional / FYI）付きのレビューコメント文面まで生成する | The Standard of Code Review / What to look for / Navigating a CL / How to write code review comments / Speed |
| `eng-split` | 要件や大きな作業を「one self-contained change」に分解。水平分割（レイヤー単位）・垂直分割（機能単位）・リファクタリングの分離・ビルドを壊さないマージ順序を含む分割計画を出力する | Small CLs |
| `eng-pr` | 変更差分から CL 説明文 / PR タイトル・本文を生成する。1 行目は命令文の自己完結した要約、本文は why と文脈（問題・選んだ理由・欠点・背景情報） | Writing good CL descriptions |

各スキルは、同梱の正典 `reference/google-eng-practices-verbatim.md` を `${CLAUDE_SKILL_DIR}` 経由で参照し、記憶や一般論ではなく**原文の記述を判断根拠**とします。

## リポジトリ構成

```text
.
└── skills/
    ├── eng-review/
    │   ├── SKILL.md
    │   └── reference/google-eng-practices-verbatim.md
    ├── eng-split/
    │   ├── SKILL.md
    │   └── reference/google-eng-practices-verbatim.md
    └── eng-pr/
        ├── SKILL.md
        └── reference/google-eng-practices-verbatim.md
```

`gh skill install` は各スキルを**個別にコピー**するため、正典は 3 スキルそれぞれに同梱しています（内容は同一）。

## 正典について

`reference/google-eng-practices-verbatim.md` は、<https://google.github.io/eng-practices/> の内容を、要約・改変を加えず元の Markdown ソース（<https://github.com/google/eng-practices>）からそのまま書き起こしたものです。

## 実行範囲

- `eng-review` / `eng-pr` は **`git diff` の読み取りまで**を行い、commit / push / PR 作成は行いません（`eng-pr` のみ、ユーザー確認後に任意で実行を提案）。
- `eng-split` は **分割計画の作成まで**を行い、実装やブランチ作成は行いません。

## ライセンス

`reference/` 内の文書は Google Engineering Practices の原文であり、[CC-BY 3.0 License](https://creativecommons.org/licenses/by/3.0/) の下にあります。
