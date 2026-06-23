---
name: eng-pr
description: Google Engineering Practices の「Writing good CL descriptions」に厳密に準拠して、変更差分から CL 説明文 / Pull Request の概要（タイトル・本文）を生成するスキル。1行目は命令文の自己完結した要約、本文は why と文脈（問題・選んだ理由・欠点・背景情報）を含める。git diff の読み取りまでを行い、コミット・push・PR 作成は行わず文面生成にとどめる（実行はユーザー確認後）。トリガー: 「eng-pr」「PR概要を作って」「CL説明文を書いて」「PRの説明を書いて」「コミットメッセージを書いて（Google基準で）」。
---

# eng-pr — Google Engineering Practices 準拠の CL 説明文 / PR 概要作成

Google の「Writing good CL descriptions」ガイドラインに**厳密に準拠**して、変更差分から CL 説明文 / PR のタイトル・本文を生成するスキル。

## 正典（Source of Truth）

文面の判断基準は必ず以下のファイルに従うこと。開始時に**必ず該当セクションを読む**こと。

```
~/.claude/skills/_eng-practices-reference/google-eng-practices-verbatim.md
```

参照すべき主なセクション（原文の見出し）:
- `Writing good CL descriptions` — 全体。特に以下のサブセクション:
  - `First Line`
  - `Body is Informative`
  - `Bad CL Descriptions`
  - `Good CL Descriptions`（Functionality change / Refactoring / Small CL that needs some context の各例）
  - `Generated CL descriptions`
  - `Review the Description Before Submitting`

## 実行フロー

### 1. 正典の読み込み

`google-eng-practices-verbatim.md` の `Writing good CL descriptions` セクションを読む。特に以下を頭に入れる:

- CL 説明文は**変更の公開記録**であり、次を伝える: **What**（何を変更したか）と **Why**（なぜ変更したか）。
- **First Line（1行目）**:
  - 何をしているかの短い要約。
  - **命令文（imperative sentence）として書かれた完全な文**。その後に空行。
  - 例: "**Delete** the FizzBuzz RPC and **replace** it with the new system."（"Deleting... and replacing..." ではない）
  - バージョン管理の履歴サマリーに表示されるため、**単独で成立**すること。短く・焦点を絞り・要点を突く。
- **Body is Informative（本文）**:
  - 詳細を埋め、CL を全体的に理解するのに必要な補足情報を含める。
  - 解決する問題の簡潔な説明、なぜこのアプローチが最善か。
  - 欠点があれば言及する。
  - 関連すればバグ番号・ベンチマーク結果・設計ドキュメントへのリンク等の背景情報。
  - **外部リンクは将来見えない可能性がある**ことを考慮し、本文に十分な文脈を含める。
  - 小さな CL でも細部に注意し、文脈の中に置く。
- **Bad CL Descriptions（避けるべき例）**: "Fix bug" / "Fix build." / "Add patch." / "Moving code from A to B." / "Phase 1." / "Add convenience functions." / "kill weird URLs." — 短すぎて有用な情報がない。

### 2. 変更内容の把握

差分を取得する。**git diff の読み取りまで**を行い、コミット・push・PR 作成は行わない。

- 対象が未指定なら確認する（未コミット変更 / ブランチ差分 / 特定コミット）。
- 代表的な取得方法:
  - ブランチ差分（PR想定）: `git diff <base>...<head>` と `git log <base>..<head> --oneline`
  - 未コミット変更: `git diff HEAD`
  - 特定コミット: `git show <commit>`
- まず `git diff --stat` で全体像を把握する。生の diff の解析には context-mode の `ctx_execute` を使い要点だけ抽出するとよい。
- 変更の **What と Why** を読み取る。Why が差分から判断できない場合は、推測で埋めず**ユーザーに確認する**（CLAUDE.md の方針: 推測で進めない）。

### 3. 文面の生成

変更の種類（機能変更 / リファクタリング / 小さいが文脈が必要、など）に応じて、原文の Good 例の型に沿って生成する。

**生成物**:
1. **CL 説明文 / PR タイトル（1行目）**:
   - 命令文・完全な文・単独で成立・短く焦点を絞る。
2. **本文**:
   - 1行目の後に空行。
   - 問題の説明 → なぜこのアプローチか → （あれば）欠点 → 背景情報（バグ番号・ベンチマーク・設計ドキュメント）。
   - 外部リンクに依存せず文脈が読み取れること。
3. **PR 用の補足**（PR を想定する場合）:
   - 変更点の要約、テスト方法、レビュアーが確認すべき点、影響範囲。
   - プロジェクトに PR テンプレート（`.github/PULL_REQUEST_TEMPLATE.md` 等）があれば、それに沿わせる（存在を確認する）。

### 4. 出力

生成した文面を Markdown ファイルとして `/tmp` に出力する（CLAUDE.md の出力規約に準拠）。ファイル名は英語のケバブケース（例: `/tmp/eng-pr-<topic>.md`）。

出力の構成:
1. **タイトル / 1行目（コピー用）**
2. **本文（コピー用）**
3. **（PR の場合）PR 本文（コピー用）**
4. **このスキルが従った原則の要約**（命令文か / 単独成立か / why を含むか / 欠点に触れたか）— セルフチェックとして提示。

出力後、`mo` がインストールされていれば `mo <file>` で開く（`which mo` で確認。なければ通常出力にフォールバック）。

### 5. 実行（任意・ユーザー確認後）

このスキルは既定では**文面生成までにとどめる**。ユーザーが明示的に望んだ場合のみ、次の操作を提案・実行する（実行前に必ず確認する）:

- `git commit`（コミットメッセージとして文面を使用）
- `git push`
- `gh pr create`（タイトル・本文として文面を使用）

> CLAUDE.md の方針に従い、push / PR 作成のような外向き・不可逆の操作は事前確認を必須とする。changeset（@changesets/cli 等）の導入有無も確認し、必要なら変更レベル（patch/minor/major）を提案する。

## 重要な心構え（原文の核心）

- 1行目は**命令文・単独成立・短い要約**。本文は **why と文脈**。
- "Fix bug" のような中身のない説明を絶対に書かない。
- **Review the Description Before Submitting** — レビュー中に CL は変わる。提出前に説明文が変更を正確に反映しているか確認する。
