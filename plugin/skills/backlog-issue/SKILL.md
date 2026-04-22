---
name: backlog-issue
description: >
  This skill should be used when the user asks to "list Backlog issues",
  "search Backlog", "find issue", "create a Backlog ticket", "update the
  status", "assign issue", "add comment to Backlog", "close the ticket",
  "show issue detail", or any workflow centered on Backlog 課題/チケット
  such as 一覧・検索・作成・更新・コメント・ステータス変更. Use it both
  for Japanese and English utterances about Backlog issues.
metadata:
  version: "0.1.0"
---

# Backlog Issue 操作

このスキルは Backlog の課題 (Issue) を検索・閲覧・作成・更新・コメントする
ためのガイド。`backlog-mcp-server` が提供する `mcp__backlog__*` ツール群を
呼び出して処理を行う。

## 基本原則

- **書き込み前に必ず確認**: 新規作成・ステータス変更・削除など永続的な
  副作用を伴う操作の前に、内容をユーザーにプレーンな文章で提示し、承認を得る
- **IDよりキーで話す**: ユーザーとの会話ではプロジェクトキー＋番号
  （例：`PROJ-123`）を使う。ツール呼び出しでは必要に応じて issueId に変換する
- **プロジェクトキーの扱い**: ユーザーが指定しなかった場合、直前のやり取りで
  使用したキーを流用する。それも無ければ `mcp__backlog__get_project_list` で
  選択肢を提示し、どのプロジェクトを対象にするか聞く

## 主要な操作パターン

### 1. プロジェクト一覧を見る

ユーザー：「Backlogのプロジェクト教えて」

- `mcp__backlog__get_project_list` を呼ぶ
- `projectKey` と `name` を表形式で返す。多い場合は上位10件＋総数

### 2. Issue 検索

ユーザー：「PROJの未完了タスク」「直近1週間で自分に割り当てられたもの」など

- まず `mcp__backlog__get_project` で対象プロジェクトの ID を取得
- 必要ならステータスID・担当者IDを事前解決：
  - `mcp__backlog__get_project_status_list`
  - `mcp__backlog__get_user_list` または `mcp__backlog__get_project_user_list`
- `mcp__backlog__get_issues`（または同等の検索系）に絞り込みパラメータを渡す
- 結果はキー・件名・ステータス・担当者・期限の表で返す
- 件数が多い場合は「絞り込み観点」を確認

### 3. Issue 詳細

ユーザー：「PROJ-123 の詳細」「この課題の全文」

- `mcp__backlog__get_issue` をキーまたは ID で呼ぶ
- 件名・ステータス・担当者・優先度・期限・説明本文・カテゴリ/バージョンを整形
- 直近のコメントは `mcp__backlog__get_issue_comments` で別途取得（デフォルト5件）

### 4. Issue 新規作成

ユーザー：「新しいタスクを作って」「〜のバグを起票して」

処理順：

1. プロジェクトキー、種別（Task/Bug/Request等）、件名、説明 を確認
2. 不足があれば最小限のAskUserQuestionで確認（または会話で質問）
3. 種別の issueTypeId は `mcp__backlog__get_project_issue_type_list` で解決
4. 優先度は既定 Normal（3）で良いか確認
5. 下書きを提示し、ユーザー承認後に `mcp__backlog__create_issue` を実行
6. 成功したら URL（`https://<domain>/view/<issueKey>`）を付けて報告

### 5. Issue 更新

ユーザー：「PROJ-123 をクローズして」「担当者を田中さんに変えて」

- `mcp__backlog__get_issue` で現状を取得
- 変更点を明示してユーザーに承認を取る
- `mcp__backlog__update_issue` を実行
- ステータス遷移が許可されない場合もあるためエラー時は丁寧に説明

### 6. コメント

ユーザー：「PROJ-123 に『確認済み』とコメント」

- 下書きを提示して承認
- `mcp__backlog__add_issue_comment` で投稿
- 既存コメントの確認には `mcp__backlog__get_issue_comments`

## 出力フォーマット

検索結果や一覧は表（Markdown）で返す。カラムは最大5つに絞る。
詳細表示は見出し付きの説明リストで返し、長文の説明はそのままコードブロックに
入れず素の本文として載せる。

URL は `https://<BACKLOG_DOMAIN>/view/<issueKey>` で構築。`BACKLOG_DOMAIN` は
`.mcp.json` を参照。

## 破壊的操作のガード

- 削除（`delete_issue`）は明示的な削除依頼があり、かつユーザーの二重確認を
  取れた場合のみ実行する
- 一括更新を求められたら、対象全件を先に列挙して承認を取る

## 参考ツール

Backlog MCP で利用可能な関連ツール（全量は `enable_toolsets: issue, project`）：

- `mcp__backlog__get_project_list` / `get_project`
- `mcp__backlog__get_issues` / `get_issue` / `get_issues_count`
- `mcp__backlog__create_issue` / `update_issue` / `delete_issue`
- `mcp__backlog__get_issue_comments` / `add_issue_comment` / `update_issue_comment`
- `mcp__backlog__get_project_status_list` / `get_project_category_list` / `get_project_issue_type_list`
- `mcp__backlog__get_user_list` / `get_project_user_list`

正確なツール名・パラメータは実際の MCP サーバーからの提供スキーマに従う。
