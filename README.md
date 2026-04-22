# cowork-backlog

Cowork から Nulab Backlog の Issue・Wiki を操作するためのプラグインマーケットプレイス。

## インストール

Cowork のプラグイン管理で、以下のリポジトリURLをマーケットプレイスとして追加：

```
https://github.com/ilu-hayashi/cowork-backlog
```

あるいは Claude Code CLI の場合：

```
/plugin marketplace add ilu-hayashi/cowork-backlog
/plugin install cowork-backlog
```

## 含まれるプラグイン

- **cowork-backlog** — Backlog Issue・Wiki 操作用の Skills と、Nulab 公式 `backlog-mcp-server` を同梱。

詳細は [`plugin/README.md`](./plugin/README.md) を参照。

## セットアップ

Windowsのユーザー環境変数に以下を設定してください：

- `BACKLOG_DOMAIN` — Backlog スペースのドメイン（例: `ilu-klaei.backlog.jp`）
- `BACKLOG_API_KEY` — Backlog の「個人設定」→「API」で発行したキー

設定後、Cowork を再起動すると `plugin/.mcp.json` から環境変数が展開されて接続されます。

## ライセンス

MIT
