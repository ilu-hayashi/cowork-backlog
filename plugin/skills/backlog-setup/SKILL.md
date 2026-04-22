---
name: backlog-setup
description: >
  This skill should be used when the user asks to "set up Backlog",
  "configure Backlog", "change Backlog credentials", "switch Backlog space",
  "check Backlog connection", or when the user reports that Backlog tools
  are not responding or returning authentication errors. Use it for the
  first-time configuration and whenever credentials need to be rotated.
metadata:
  version: "0.1.0"
---

# Backlog セットアップ

このスキルは Backlog MCP サーバー (`backlog-mcp-server`) の接続確認と
認証情報の更新を支援する。プラグインには初期認証情報があらかじめ埋め込まれて
いるが、別スペース/別ユーザーに切り替える場合はこのスキルで対応する。

## 接続確認の手順

ユーザーが接続を確認したいと言ったら、次の順で動作確認する。

1. `mcp__backlog__get_myself` を呼び、現在認証されているユーザー情報を取得する
2. 結果が返れば接続成功として、ユーザー名・メール・言語設定を要約して報告する
3. エラーが返った場合は、エラーメッセージを元に原因を特定する（下記参照）

## 認証情報の変更

ユーザーが認証情報を変更したいと言ったら、次を行う。

1. `${CLAUDE_PLUGIN_ROOT}/.mcp.json` を Read する
2. `BACKLOG_DOMAIN` または `BACKLOG_API_KEY` の値を Edit で書き換える
3. 「変更後、Cowork を一度再起動すると新しい認証情報で接続されます」と伝える

ドメイン形式：`<space名>.backlog.jp` または `<space名>.backlog.com`。
フルURL（`https://…`）ではなくホスト名だけを入れる。

API キーは Backlog の「個人設定」→「API」タブから発行できる。

## デフォルトプロジェクトの管理

ユーザーが「主に使うプロジェクトはこれ」と伝えてきたら、本スキルではなく
`backlog-issue` や `backlog-wiki` スキルの先頭にあるメモリ欄を使うよう案内する。
プラグイン自身は特定のプロジェクトに縛られない設計。

具体的には、ユーザーがプロジェクトキーを初めて教えてくれたタイミングで、
会話内で覚えておき、以後の Backlog 操作で明示的に渡されなければそのキーを
デフォルトとして用いる。`mcp__backlog__get_project_list` で正規化された
`projectKey` を確認しておくと齟齬が減る。

## トラブルシューティング

| 症状 | 原因と対処 |
|------|-----------|
| Authentication failed / 401 | `BACKLOG_API_KEY` を再確認。Backlog 側でキーが失効している可能性あり |
| Invalid domain / DNS | `BACKLOG_DOMAIN` を再確認。プロトコル (`https://`) は付けない |
| `npx` がパッケージ取得に失敗 | ネットワークまたは npm レジストリの問題。時間を置いて再試行 |
| Cowork にツールが現れない | プラグインの有効化確認と Cowork 再起動 |

## やらないこと

- API キーを会話ログに平文で繰り返し出力しない（`***` でマスクする）
- `.mcp.json` 以外の場所に認証情報を書かない
