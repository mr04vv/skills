# skills

[Google Engineering Practices](https://google.github.io/eng-practices/) に準拠した Claude Code 用スキル集。

コードレビュー文化（The Code Reviewer's Guide / The Change Author's Guide）の原則に沿って、レビュー・作業分割・PR 文面作成を支援します。すべてのスキルは原文（`_eng-practices-reference/`）を正典（source of truth）として参照します。

## スキル一覧

| スキル | 役割 | 準拠する原文ページ |
| --- | --- | --- |
| [`eng-review`](./eng-review/SKILL.md) | git diff / ブランチ差分 / コミットを Design・Functionality・Complexity・Tests・Naming・Comments・Style・Documentation の 8 観点でレビューし、礼儀正しく重要度ラベル（Nit / Optional / FYI）付きのレビューコメント文面まで生成する | The Standard of Code Review / What to look for / Navigating a CL / How to write code review comments / Speed |
| [`eng-split`](./eng-split/SKILL.md) | 要件や大きな作業を「one self-contained change」に分解。水平分割（レイヤー単位）・垂直分割（機能単位）・リファクタリングの分離・ビルドを壊さないマージ順序を含む分割計画を出力する | Small CLs |
| [`eng-pr`](./eng-pr/SKILL.md) | 変更差分から CL 説明文 / PR タイトル・本文を生成する。1 行目は命令文の自己完結した要約、本文は why と文脈（問題・選んだ理由・欠点・背景情報） | Writing good CL descriptions |

## リファレンス（正典）

- [`_eng-practices-reference/google-eng-practices-verbatim.md`](./_eng-practices-reference/google-eng-practices-verbatim.md)
  - <https://google.github.io/eng-practices/> の内容を、要約・改変を加えず元の Markdown ソース（<https://github.com/google/eng-practices>）からそのまま書き起こしたもの。
  - 各スキルは実行時にこのファイルの該当セクションを参照し、記憶や一般論ではなく原文の記述を判断根拠とする。

## インストール

各スキルディレクトリを Claude Code のスキル置き場（例: `~/.claude/skills/`）に配置します。

```sh
git clone git@github.com:mr04vv/skills.git
cp -r skills/eng-review skills/eng-split skills/eng-pr skills/_eng-practices-reference ~/.claude/skills/
```

スキルは名前（`/eng-review` 等）か、説明文のトリガー語（「Google 流レビューして」「要件を分解して」「PR 概要を作って」等）で起動します。

## 実行範囲

- `eng-review` / `eng-pr` は **`git diff` の読み取りまで**を行い、commit / push / PR 作成は行いません（`eng-pr` のみ、ユーザー確認後に任意で実行を提案）。
- `eng-split` は **分割計画の作成まで**を行い、実装やブランチ作成は行いません。

## ライセンス

`_eng-practices-reference/` 内の文書は Google Engineering Practices の原文であり、[CC-By 3.0 License](https://creativecommons.org/licenses/by/3.0/) の下にあります。
