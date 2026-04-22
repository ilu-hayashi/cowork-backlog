# cowork-backlog

Cowork から Nulab Backlog の Issue・Wiki を操作するためのプラグインマーケットプレイス。

## インストール

Cowork のプラグイン管理で、以下のリポジトリURLをマーケットプレイスとして追加：

```
https://github.com/kentaro/cowork-backlog
```

あるいは Claude Code CLI の場合：

```
/plugin marketplace add kentaro/cowork-backlog
/plugin install cowork-backlog
```

## 含まれるプラグイン

- **cowork-backlog** — Backlog Issue・Wiki 操作用の Skills と、Nulab 公式 `backlog-mcp-server` を同梱。

詳細は [`plugin/README.md`](./plugin/README.md) を参照。

## セットアップ

`plugin/.mcp.json` に `BACKLOG_DOMAIN` と `BACKLOG_API_KEY` を設定してください。

## ライセンス

MIT
