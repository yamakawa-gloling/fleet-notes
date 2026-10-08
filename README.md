# fleet-notes

Agent Fleet を個人やチームで使うときの使い方をまとめた文書を置く。実装は持たない。

Agent Fleet は、Claude Code や Codex などのコーディングエージェントをサーバー上で動かし、
ブラウザから操作する Web コンソールである。本体は OSS として
[k-k1/agent-fleet](https://github.com/k-k1/agent-fleet) で公開されている。ここでは、
デプロイメントが既に用意されていて、メンバーがブラウザからログインして使う場面を前提にする。

| ファイル | 中身 |
|---|---|
| `docs/start.md` | ログインと初期設定、clone、ワークスペースの環境、一日の終わり |
| `docs/sessions.md` | セッションの起動・応答・片付け、並行作業、共有 |
| `docs/code.md` | コミットと push、`af-db`、動かしたアプリの開き方 |
| `docs/without-docker.md` | Docker 前提のリポジトリを Fleet で立てる試し方（未検証の計画） |
| `docs/personal-config.md` | 手元の Claude Code 環境を Fleet で使って試したこと、Fleet 側の仕組み、自分の使い方、使ってみて感じたこと |
| `docs/ops/doc-style.md` | このリポの文書の書き方 |

`docs/` の使い方は本家の利用ガイド（`guide/member/`）から抜き出したもので、全文は本家にある。
`docs/personal-config.md` と `docs/without-docker.md` は、使ってみて確かめたことや試す計画、本家のコードを読んだ結果で、本家のガイドには無い。
