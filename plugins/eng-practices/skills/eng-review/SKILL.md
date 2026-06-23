---
name: eng-review
description: "Google Engineering Practices（コードレビューガイドライン）に厳密に準拠してコードレビューを行うスキル。未コミット変更・ブランチ差分・特定コミットを Design/Functionality/Complexity/Tests/Naming/Comments/Style/Documentation の観点でレビューし、礼儀正しく重要度ラベル（Nit/Optional/FYI）付きのレビューコメントを生成する。トリガー: 「eng-review」「Google流レビュー」「eng practices でレビュー」「コードレビューして（Google基準で）」。"
---

# eng-review — Google Engineering Practices 準拠コードレビュー

Google の公式 Code Review ガイドライン（The Code Reviewer's Guide）に**厳密に準拠**してコードレビューを行うスキル。指摘を見つけるだけでなく、そのまま使える礼儀正しいレビューコメント文面まで生成する。

## 正典（Source of Truth）

レビューの判断基準は必ず以下のファイルに従うこと。レビュー開始時に**必ず該当セクションを読む**こと。記憶や一般論ではなく、原文の記述を根拠にする。

```
${CLAUDE_PLUGIN_ROOT}/reference/google-eng-practices-verbatim.md
```

> このスキルはプラグイン `eng-practices` に同梱されており、正典はプラグインルート配下の `reference/` にある。`${CLAUDE_PLUGIN_ROOT}` はプラグインのルートディレクトリに解決される環境変数。実行時にこの変数が展開されない場合は、このスキル自身のディレクトリ（`${CLAUDE_SKILL_DIR}`）から見て `../../reference/google-eng-practices-verbatim.md` を参照する。

参照すべき主なセクション（原文の見出し）:
- `The Standard of Code Review` — レビューの最上位ルールと原則
- `What to look for in a code review` — 8観点の詳細
- `Navigating a CL in review` — レビューの進め方（順序）
- `How to write code review comments` — コメントの書き方・重要度ラベル
- `Speed of Code Reviews` — LGTM の意味、対応速度

## 実行フロー

### 1. 正典の読み込み

`google-eng-practices-verbatim.md` のうち、上記の主要セクションを読む。特に以下を頭に入れる:

- **最上位ルール**: "In general, reviewers should favor approving a CL once it is in a state where it definitely improves the overall code health of the system being worked on, even if the CL isn't perfect."（完璧でなくても、確実にコードヘルスを改善するなら承認に傾ける）
- **完璧主義の禁止**: 改善できる点は多数あるが「絶対に必要」なものだけを必須にし、それ以外は任意（Nit:）とする。
- **原則**: 技術的事実・データ > 意見。スタイルは style guide が絶対権威。設計は原則で判断。個人的好みで CL をブロックしない。

### 2. レビュー対象の特定

ユーザーの指定に応じて差分を取得する。**git diff の読み取りまで**を行い、コミットや push はしない。

- 指定がなければ、まず対象を確認する（未コミット変更か、ブランチ差分か、特定コミットか）。
- 代表的な取得方法:
  - 未コミット変更: `git diff HEAD`（ステージ済み含む場合は `git diff --staged` も併用）
  - ブランチ差分: `git diff <base>...<head>`（例: `git diff main...HEAD`）
  - 特定コミット: `git show <commit>`
- まず変更の全体像（変更ファイル一覧・統計）を `git diff --stat` 等で把握する。

> 大きな差分の解析には context-mode の `ctx_execute`（language: shell / javascript）を使い、生の diff を会話に取り込まず要点だけ抽出するとよい。

### 3. CL のナビゲート（Navigating a CL in review に準拠）

原文の手順どおりに進める:

1. **Take a broad view of the change** — 変更全体が理にかなっているか。そもそもこの変更を行うべきでないなら、その旨を最初に（理由・代替案・作者の労力への配慮とともに）伝える。
2. **Examine the main parts of the CL** — 論理的変更が最も多い「メイン」ファイルを最初に見る。メインに重大な設計問題があれば、残りより先にそれを指摘する。
3. **Look through the rest in an appropriate sequence** — 残りを見落としなく適切な順序で。テストを先に読むと変更意図が掴めることがある。

**Every Line**: 原則として割り当てられた全行を見る（自動生成・データファイル等はスキャン可）。理解できないコードは「他の開発者も理解できない可能性が高い」ので明確化を求める。

### 4. 8観点でのレビュー（What to look for に準拠）

各観点で指摘を洗い出す。**常に The Standard を念頭に置く**（コードヘルスを確実に改善するか）。

1. **Design** — 最重要。コード同士の相互作用、システムへの適合、追加すべき場所・タイミング。
2. **Functionality** — 作者の意図どおり動くか。ユーザー（エンドユーザー＋将来の開発者）にとって良いか。並行処理・エッジケースに注意。UI 変更は実際の見た目を確認。
3. **Complexity** — 必要以上に複雑でないか（行/関数/クラス各レベル）。"Too complex" = 素早く理解できない / 変更時にバグを入れやすい。**Over-engineering（将来用の汎用化・未使用機能）に特に警戒**。「今解決すべき問題を解け」。
4. **Tests** — 適切で、よく設計された自動テストがあるか。テストもコードであり複雑すぎてはならない。
5. **Naming** — 何であるか/何をするかを十分に伝える名前か（長すぎない範囲で）。
6. **Comments** — 明確で有用か。コメントは原則 "why" を説明する（"what"/"how" ではない）。将来作業は TODO（追跡可能な形）と区別。
7. **Style** — style guide に準拠しているか。ガイドにない改善提案は `Nit:` を付ける。大きなスタイル変更を他の変更と混ぜない。
8. **Consistency** — style guide が絶対権威。要求事項なら従う。推奨どまりなら周囲との一貫性と天秤。既存コード掃除はバグ登録＋TODO を促す。
9. **Documentation** — 手順やコードの増減に応じて関連ドキュメント（README/g3doc 等）が更新されているか。

加えて:
- **Context** — 数行の差分だけでなく広い文脈で見る（例: メソッドが肥大化していないか）。
- **Good Things** — 良い点があれば褒める（特にコメントへの見事な対応）。指導上、正しい点を伝える価値は大きい。

### 5. レビューコメントの文面生成（How to write code review comments に準拠）

見つけた指摘を、そのまま使えるコメント文面にする。必須ルール:

- **Be kind / Courtesy** — 常に**コードについて**コメントし、**人について**コメントしない。
  - 悪い例: "Why did **you** use threads here when there's obviously no benefit...?"
  - 良い例: "The concurrency model here is adding complexity to the system without any actual performance benefit that I can see. Because there's no performance benefit, it's best for this code to be single-threaded instead of using multiple threads."
- **Explain your reasoning** — なぜその指摘をするのか理由を添える。
- **Giving Guidance** — 問題を直す責任は作者にある。問題の指摘と、直接的な指示のバランスを取る。詳細な解決策を必ずしも与えなくてよい。
- **Label comment severity** — 重要度を明示する。必須でない指摘には必ずラベルを付ける:
  - `Nit:` 些細。技術的にはやるべきだが影響は小さい。
  - `Optional (or Consider):` 良い考えだと思うが必須ではない。
  - `FYI:` この CL でやってほしいわけではないが、将来のために。
- **Mentoring** — 教育目的だが必須でないコメントは `Nit:` 等を付けて必須でないと示す。

### 6. 出力

レビュー結果を Markdown ファイルとして `/tmp` に出力する（CLAUDE.md の出力規約に準拠）。ファイル名は内容を端的に表す英語のケバブケース（例: `/tmp/eng-review-<topic>.md`）。

出力の構成:
1. **Overall assessment** — The Standard に照らした総合判断。「コードヘルスを確実に改善するか → 承認方向か、要修正か」。完璧を求めていないことを明記。
2. **Blocking issues（必須）** — マージ前に対応が必要な指摘。ファイル:行を `file_path:line` 形式で示し、各指摘に**そのまま貼れるコメント文面**を添える。
3. **Non-blocking（Nit / Optional / FYI）** — 重要度ラベル付きの任意指摘。
4. **Good things** — 褒めるべき点。
5. 観点ごとに整理（Design / Functionality / ... / Documentation）。

出力後、`mo` がインストールされていれば `mo <file>` で開く（`which mo` で確認。なければ通常出力にフォールバック）。

## 重要な心構え（原文の核心）

- **完璧ではなく継続的改善**。「自分ならこう書く」を CL に強制しない。
- 個人的好みで CL をブロックしない。技術的事実・データ・原則で語る。
- 「LGTM」は「このコードは我々の基準を満たす」を意味すると確信できるときだけ使う。
- 対立時は合意を目指し、解決しなければ The Standard の原則に立ち返る（このスキルは指摘の提示までを担い、対立解決はユーザーに委ねる）。
