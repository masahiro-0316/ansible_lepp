ロール名
=========

PostgreSQLのインストール

必要条件
------------

ロール変数
--------------

依存関係
------------

- PostgreSQL 14
- Rocky 8

なし

使用例のPlaybook
----------------

ライセンス
-------

著者情報
------------------

PostgreSQL手動インストール
---------

1. postgrasql のリポジトリ登録

```bash
sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-$(rpm -E %{rhel})-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

timescale用のリポジトリ登録

```bash
sudo tee /etc/yum.repos.d/timescale_timescaledb.repo <<EOL
[timescale_timescaledb]
name=timescale_timescaledb
baseurl=https://packagecloud.io/timescale/timescaledb/el/$(rpm -E %{rhel})/\$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/timescale/timescaledb/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
EOL
```

リポジトリを更新
```bash
sudo dnf check-update -y
```

1. データベースインストール

timescaleDB(postgrasql)をインストールする。

```bash
sudo dnf install -y timescaledb-2-postgresql-14
```
> 失敗する場合は一度以下のコマンドを実行する
> ```bash
> sudo dnf -qy module disable postgresql 
> ```

データベースの初期化
```bash
sudo /usr/pgsql-14/bin/postgresql-14-setup initdb
```
実行結果

```bash
Initializing database ...
OK
```

1. データベース初期後の確認

データベース初期設定のログを確認する

```bash
sudo cat /var/lib/pgsql/14/initdb.log
```

サービスを起動してステータスを確認

```bsah
sudo systemctl enable --now postgresql-14.service
sudo systemctl status postgresql-14.service
```

`active`になっていることを確認

```bash
● postgresql-14.service - PostgreSQL 14 database server
   Loaded: loaded (/usr/lib/systemd/system/postgresql-14.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2024-03-15 22:36:29 JST; 4s ago
     Docs: https://www.postgresql.org/docs/14/static/
  Process: 134414 ExecStartPre=/usr/pgsql-14/bin/postgresql-14-check-db-dir ${PGDATA} (code=exited, status=0/SUCCESS)
 Main PID: 134419 (postmaster)
    Tasks: 8 (limit: 23129)
   Memory: 16.8M
   CGroup: /system.slice/postgresql-14.service
 
````

postgresユーザへの切り替え

```bash
sudo su - postgres
```

PostgraSQLの作動確認

データベースユーザーの確認

```bash
psql -c "select usename from pg_user;"
```

データベースの確認

```bash
psql -l 
```
