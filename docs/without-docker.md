# Docker 前提のリポジトリを Fleet で立てる

ローカル開発を `docker compose` 前提で組んだリポジトリを、Docker の無い Fleet のワークスペースで
立ち上げて画面まで開く。リポジトリの側に Docker を使わない立て方とプレビューの入口を用意しておき、
Fleet ではその入口を呼ぶだけにする。

## リポジトリの側に用意するもの

Fleet の側だけでは解決しないので、先にリポジトリで次の2つを用意する。

**Docker を使わない立て方。** スクリプトが DB の接続先を環境変数で受け取り、その変数の有無で compose と
外部の DB に分かれるようにする。MySQL なら `MYSQL_HOST` を鍵にし、ポート・管理ユーザー・DB 名も
環境変数で渡す。dev サーバーと API のポート、listen 先も環境変数で変えられるようにする。手順は README に書く。

**プレビューの入口。** 一式を立てて画面の開き方を示す Claude Code のスキルを置き、上と同じ鍵で分岐させる。
Docker の有無を `docker info` だけで判断すると、Fleet では最初の段で止まる。Docker を使わない側では次のように振る舞わせる。

- `docker` コマンドが無ければ、つなぐ DB を user に尋ねる
- 受け取った接続先をファイル（`.env` など）に書いて残す。Claude Code の Bash は呼び出しをまたぐと環境変数が消える
- dev サーバーを `127.0.0.1` で listen させる
- 開き方は `localhost` の URL ではなく、ポート番号で示す。ブラウザはワークスペースの外にある

## 置き換え

| compose のサービス | Fleet での置き換え |
|---|---|
| MySQL 8.4 | `af-db up mysql`（ポートは 3306 で固定）。接続先は `af-db url mysql --tcp` で分かる |
| S3 互換のストレージ | 用意しない。S3 を使う機能と、S3 を要るデータの投入は動かない |
| Python のプロセス | 仮想環境（`uv venv`）に依存を入れて直接起動する |
| API・画面 | ワークスペースの中で直接起動する |

MySQL のトリガは、SUPER 権限か `log_bin_trust_function_creators=1` が無いと作れない。compose では
MySQL の起動引数で後者を満たしていることが多い。管理ユーザーで足りなければ、
`SET GLOBAL log_bin_trust_function_creators = 1` を先に流す。

`/tmp` が実行不可の環境では、`go run`・`go test` が `permission denied` で落ちる。そのときは
`GOTMPDIR` に実行できるディレクトリを渡す。

## Fleet で開く

1. リポジトリを clone した作業コピーでセッションを起動し、`af-db up mysql` で MySQL を立てる
2. プレビューの入口を呼ぶ。つなぐ DB を聞かれたら、`af-db url mysql --tcp` の値を渡す
3. 画面が立ったら、ワークスペース操作バーのポート入力欄に dev サーバーのポートを入れ、ペインで開く
   （`docs/code.md`「動かしたアプリを開く」）

Vite は既定で `localhost` に listen し、IPv6 の `::1` だけで待つことがある。`--host 127.0.0.1` を付けて
起動する。プレビュー用サブドメインで開くと、Vite が知らないホスト名を断ることがある（`server.allowedHosts`）。

## 試した結果

本家のガイドには無く、使ってみて確かめたことである。

- 上の形で、compose 前提のリポジトリの画面を Fleet のペインで開けた
- DB を使うテストの全通過と、Python のプロセスが作るファイルが API 経由で返るかは確かめていない
