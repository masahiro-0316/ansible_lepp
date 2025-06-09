postgresql
=========

PostgreSQL サーバをインストールし、初期化・設定を行い、最後に Goss で基本動作検証を行います。

要件
------------

- Ansible 2.9 以上  
- 対象ホストが RedHat 系ディストリビューション（EL8, EL9, Rocky8 など）  
- 制御ノードに Goss バイナリが `/usr/local/bin/goss` に配置されていること  
- 対象ホストがインターネット接続可能、または PGDG リポジトリ RPM（`pgsql_repo`）をダウンロード可能であること  
- systemd が利用可能であること  

ロール変数
--------------

**defaults/main.yml**

| 変数名               | 説明                                                      | デフォルト値                                        |
| -------------------- | --------------------------------------------------------- | --------------------------------------------------- |
| `pgsql_version`      | インストールする PostgreSQL のバージョン                   | `16`                                                |
| `pgsql_dir`          | PostgreSQL データディレクトリのベースパス                  | `/var/lib/pgsql`                                    |
| `pgsql_initlog_path` | `initdb` 実行ログファイルのパス                           | `{{ pgsql_dir }}/{{ pgsql_version }}/initdb.log`   |

**vars/RedHat.yml**

| 変数名                        | 説明                                                      | デフォルト値                                                                                                            |
| ----------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `pgsql_repo`                  | PGDG 公式 YUM リポジトリ RPM の URL                        | `https://download.postgresql.org/pub/repos/yum/repor...{{ ansible_distribution_major_version }}-x86_64/pgdg-redhat-repo-latest.noarch.rpm` |
| `timescaledb_repo_baseurl`    | TimescaleDB リポジトリのベース URL                         | `https://packagecloud.io/timescale/timescaledb/el/{{ ansible_distribution_major_version }}/$basearch`                    |
| `timescaledb_repo_gpgkey`     | TimescaleDB リポジトリの GPG キー URL                      | `https://packagecloud.io/timescale/timescaledb/gpgkey`                                                                  |
| `timescaledb_ssl_path`        | SSL 証明書バンドルのパス                                   | `/etc/pki/tls/certs/ca-bundle.crt`                                                                                     |
| `timedb2pgsql`                | インストールする TimescaleDB と Psycopg2 のパッケージ名リスト | `['timescaledb-2-postgresql-{{ pgsql_version }}', 'python3-psycopg2']`                                                   |

**vars/main.yml**

| 変数名            | 説明                                              | デフォルト値                             |
| ----------------- | ------------------------------------------------- | ---------------------------------------- |
| `goss_test_dir`   | Goss テスト用に一時的にファイルを配置するディレクトリ | `/var/tmp/{{ role_name }}_gosstest`      |
| `goss_test_files` | Goss テストに用いるファイル情報（src, dest, owner 等） | 下記のリスト参照                          |

```yaml
goss_test_files:
  - local_file: /usr/local/bin/goss
    remote_file: "{{ goss_test_dir }}/goss"
    own: root
    gro: root
    mod: 755
````

## 依存関係

なし

## サンプル Playbook

```yaml
- hosts: db_servers
  become: true
  roles:
    - role: postgresql
      pgsql_version: 13
      pgsql_dir: /data/pgsql
```

## ライセンス

This role is licensed under the Apache License, Version 2.0.
See [LICENSE](../../LICENSE) for details.
