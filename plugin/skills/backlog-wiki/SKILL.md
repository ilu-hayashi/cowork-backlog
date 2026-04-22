---
name: backlog-wiki
description: >
  This skill should be used when the user asks to "show Backlog wiki",
  "list wiki pages", "find wiki", "edit wiki", "update wiki page",
  "create wiki page", or mentions Backlog Wiki in Japanese/English —
  「Wiki一覧」「Wikiを読んで」「Wikiを更新して」「Wikiに追記」など。
metadata:
  version: "0.1.0"
---

# Backlog Wiki 操作

このスキルは Backlog の Wiki ページを一覧・参照・作成・編集するためのガイド。
`mcp__backlog__*` ツール群を利用する。

## 基本原則

- **Wiki はプロジェクト配下**: まずプロジェクトキーを特定する。会話文脈で
  明らかでなければ、`mcp__backlog__get_project_list` で候補提示
- **本文の保全**: 更新時は既存本文を必ず先に取得し、差分ベースで編集する。
  全文置換でユーザーの意図しない削除が起きないよう注意する
- **編集は承認必須**: 新規作成・本文更新・削除の前に、最終文面をユーザーに
  提示して承認を得る

## 主要な操作パターン

### 1. Wiki 一覧

ユーザー：「PROJ の Wiki 一覧」

- プロジェクトキーまたは ID を特定する
- `mcp__backlog__get_wiki_pages` を呼ぶ（必要に応じて `keyword` で絞り込み）
- 結果はページID・名前・更新日時の表で返す

### 2. Wiki 本文の参照

ユーザー：「〜〜というWikiを見せて」「手順書を教えて」

- 一覧から候補を絞り込むか、直接 `mcp__backlog__get_wiki_page` で取得
- 長文の場合は見出しごとに整形して提示。ユーザーが要約を求めた場合のみ要約
  （原文改変はしない）

### 3. Wiki の新規作成

ユーザー：「〜〜という新規Wikiを作って」

1. ページ名、本文内容、公開設定を確認
2. 下書きを提示し、承認を得る
3. `mcp__backlog__add_wiki_page` で作成
4. 作成後のページURL（`https://<domain>/wiki/<projectKey>/<encodedName>`）を返す

### 4. Wiki の編集

ユーザー：「Wiki の〜〜セクションを更新して」

1. `mcp__backlog__get_wiki_page` で現在の本文を取得
2. 変更箇所を明確にして差分を提示
3. ユーザー承認後に `mcp__backlog__update_wiki_page` で更新
4. 変更通知（メール/Slack）を送るかどうかを確認

### 5. Wiki の削除

- 明示的な削除依頼があり、ユーザーの二重確認を取れた場合のみ `delete_wiki_page`
  を呼ぶ。

## 出力フォーマット

- 一覧は Markdown 表で最大20行、それ以上は件数とともに「表示しきれていない」
  ことを明示
- 本文表示は見出し構造を保って整形。Backlog 記法はそのまま保持する
  （Markdown 記法に勝手に変換しない）

## 参考ツール

- `mcp__backlog__get_wiki_pages` / `get_wikis_count` / `get_wiki_page`
- `mcp__backlog__add_wiki_page` / `update_wiki_page` / `delete_wiki_page`
- `mcp__backlog__get_wiki_page_history` / `get_wiki_page_star`

ツール名と引数は実際の MCP サーバーが返すスキーマに従う。
