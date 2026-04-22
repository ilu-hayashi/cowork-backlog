# cowork-backlog

Cowork から Nulab Backlog の Issue・Wiki を操作するためのプラグイン。

## 概要

本プラグインは Nulab 公式の [`backlog-mcp-server`](https://www.npmjs.com/package/backlog-mcp-server) を
`npx` 経由で起動し、Backlog API との通信を担当させる。その上に、Cowork で使
いやすい一連の Skills を用意している。

## 含まれるコンポーネント

| 種別 | 名前 | 役割 |
|------|------|------|
| MCP | `backlog` | `backlog-mcp-server` を stdio で起動。Issue / Project / Wiki のツールを公開 |
| Skill | `backlog-setup` | 接続確認、認証情報の変更手順 |
| Skill | `backlog-issue` | Issue の検索・参照・作成・更新・コメント |
| Skill | `backlog-wiki` | Wiki の一覧・参照・編集・作成 |
| Skill | `backlog-issue-key-detect` | 会話中の `PROJ-123` 形式のキーを自動検出して情報取得 |

## セットアップ

プラグインを Cowork にインストールすると、同梱の `.mcp.json` に書かれた
Backlog MCP サーバーが自動で登録される。初回起動時、`npx` が自動的に
`backlog-mcp-server` を取得するため、初回のみ10〜30秒程度の起動時間がかかる。

### 認証情報

`.mcp.json` 内の環境変数に、対象スペースの情報が埋め込まれている：

```json
{
  "env": {
    "BACKLOG_DOMAIN": "<space>.backlog.jp",
    "BACKLOG_API_KEY": "<api-key>"
  }
}
```

別のスペースや別ユーザーに切り替えたい場合は、この2つの値を書き換えて
Cowork を再起動する。API キーは Backlog の「個人設定」→「API」タブで発行
できる。

## 使い方

Cowork のチャットで自然言語で指示すれば、該当する Skill が自動的に読み込
まれる。例：

- 「Backlogのプロジェクト一覧を見せて」
- 「PROJ プロジェクトの未完了タスクを一覧で」
- 「PROJ-42 の詳細を教えて」
- 「PROJ-42 に『確認済み』とコメントして」
- 「新しいタスクを作って：件名〜〜」
- 「Wikiの『開発手順』を見せて」
- 「Wikiの『リリースノート』に今回の変更を追記」

`PROJ-123` のような Issue キーが会話中に含まれると、`backlog-issue-key-detect`
が自動で基本情報を取得し、回答の文脈として補足する。

## 安全性

- 書き込み系の操作（Issue 作成、ステータス変更、Wiki 更新、削除など）は、
  Skill 側で必ずユーザー承認を取る設計
- 削除操作は二重確認が必要
- API キーは `.mcp.json` にのみ保存し、会話ログには平文で繰り返さない
  （マスク表示する）

## 利用可能なツールセット

`backlog-mcp-server` は以下のツールセットを提供する：

- `space` — Backlog スペースの設定・情報
- `project` — プロジェクト・カテゴリ・カスタムフィールド・Issue 種別
- `issue` — 課題とそのコメント
- `wiki` — Wiki ページ
- `git` — Git リポジトリ・プルリクエスト（本プラグインでは Skill 未整備）
- `notifications` — 通知

デフォルトでは全ツールセットが有効。必要に応じて `.mcp.json` の
`args` に `--enable-toolsets` を追加して絞り込める。

## トラブルシューティング

**Q: Backlog ツールが見つからない**
- プラグインが有効化されているか Cowork の設定で確認
- Cowork を再起動
- `npx` の初回ダウンロード中の可能性あり（少し待つ）

**Q: 認証エラー**
- `.mcp.json` の `BACKLOG_API_KEY` を Backlog 側で再発行して更新
- `BACKLOG_DOMAIN` にプロトコルや末尾スラッシュが混入していないか確認

**Q: 特定のツールだけ権限エラー**
- Backlog のユーザーロール（管理者/一般ユーザー/閲覧専用）による制限。
  ロールを確認

## ライセンス

MIT

## 関連

- Nulab 公式 MCP サーバー: [nulab/backlog-mcp-server](https://github.com/nulab/backlog-mcp-server)
- Backlog API リファレンス: [nulab.com/help/api](https://developer.nulab.com/docs/backlog/)
