---
title: "Docker compose で mysql:8.0 の初期化失敗"
emoji: "🐳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["mysql", "docker"]
published: true
---

原因がわからんけど動いた、という感じの物。

docker compose 環境で mysql:8.0 を動かしたときに `'./ibdata1' could not be found in the doublewrite buffer.` とか言われて動かなくなるヤツの対処。

# 環境

- ホスト: Mac OS Sonoma, (Intel/M3)
- Docker Client 26.1.3
- Docker Server 26.1.4

## Docker-compose

```yaml
version: "3.8"
services:
  mysql:
    image: mysql:8.0
    restart: always
    container_name: mysql
    ports:
      - "3306:3306"
    volumes:
      - mysqldata:/var/lib/mysql
      - ./mysql/init.d:/docker-entrypoint-initdb.d
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: hogemax
      MYSQL_USER: homestead
      MYSQL_PASSWORD: secret
      TZ: Asia/Tokyo
    networks:
      - my-local-net

volumes:
  mysqldata:
    driver: local
networks:
  my-local-net:
    external: true

```


# エラー

```
mysql    | 2024-06-24T07:13:15.786137Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.37) starting as process 1
mysql    | 2024-06-24T07:13:15.788783Z 1 [System] [MY-011012] [Server] Starting upgrade of data directory.
mysql    | 2024-06-24T07:13:15.788795Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
mysql    | 2024-06-24T07:13:15.795103Z 1 [ERROR] [MY-012224] [InnoDB] Tablespace flags are invalid in datafile: ./ibdata1, Space ID:0, Flags: 21. Please refer to http://dev.mysql.com/doc/refman/8.0/en/innodb-troubleshooting-datadict.html for how to resolve the issue.
mysql    | 2024-06-24T07:13:15.795117Z 1 [ERROR] [MY-012237] [InnoDB] Corrupted page [page id: space=0, page number=0] of datafile './ibdata1' could not be found in the doublewrite buffer.
mysql    | 2024-06-24T07:13:15.795123Z 1 [ERROR] [MY-012930] [InnoDB] Plugin initialization aborted with error Data structure corruption.
mysql    | 2024-06-24T07:13:16.298945Z 1 [ERROR] [MY-011013] [Server] Failed to initialize DD Storage Engine.
mysql    | 2024-06-24T07:13:16.299451Z 0 [ERROR] [MY-010020] [Server] Data Dictionary initialization failed.
mysql    | 2024-06-24T07:13:16.299495Z 0 [ERROR] [MY-010119] [Server] Aborting
mysql    | 2024-06-24T07:13:16.300948Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.37)  MySQL Community Server - GPL.
```

# 対処

volume 名を `mysqldata` から `mysql_data` に変更したら動いた。

原因不明。

## 変更後

```yaml
version: "3.8"
services:
  mysql:
    image: mysql:8.0
    restart: always
    container_name: mysql
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./mysql/init.d:/docker-entrypoint-initdb.d
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: hogemax
      MYSQL_USER: homestead
      MYSQL_PASSWORD: secret
      TZ: Asia/Tokyo
    networks:
      - my-local-net

volumes:
  mysql_data:
    driver: local
networks:
  my-local-net:
    external: true

```
