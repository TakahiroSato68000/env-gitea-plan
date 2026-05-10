# WSL2 + Gitea 構築手順

## 1. 目的

Windows 上の WSL2 Linux 環境を利用して Gitea を構築し、初期のソース管理基盤を整備する。

本手順では、Docker Desktop は使用せず、WSL2 上の Linux 側 Engine を用いる前提とする。

---

## 2. 前提条件

- Windows マシンを利用可能であること
- 管理者権限を有していること
- WSL2 を有効化できること
- WSL2 上で Ubuntu 等の Linux ディストリビューションを利用できること
- 社内で利用するポート方針を事前に整理できること

---

## 3. 構成概要

### Windows 側

- WSL2 を有効化する
- Gitea 利用者は Windows のブラウザからアクセスする
- 必要に応じて Windows 側で runner を動作させる

### WSL2 Linux 側

- Linux 側 Engine によるコンテナ実行環境
- Gitea
- 必要に応じて関連設定ファイルや永続データ

---

## 4. 事前準備

### 4.1 WSL2 の有効化

PowerShell を管理者で開き、以下を実行する。

```powershell name=enable-wsl.ps1
wsl --install
```

必要に応じて再起動する。

確認:

```powershell name=check-wsl.ps1
wsl --status
wsl -l -v
```

### 4.2 Linux ディストリビューションの準備

- Ubuntu を導入する
- 初回起動後にユーザーを作成する
- 更新を実施する

例:

```bash name=update-ubuntu.sh
sudo apt update
sudo apt upgrade -y
```

---

## 5. Linux 側 Engine の準備

### 5.1 方針

- Docker Desktop は使用しない
- WSL2 上の Linux 側で Engine を利用する
- Gitea は Linux 側でコンテナとして起動する

### 5.2 Linux 側での確認事項

- `docker` コマンドが利用できること
- `docker compose` が利用できること

確認例:

```bash name=check-docker.sh
docker version
docker compose version
```

※ Engine の導入方法は、利用する Linux ディストリビューションおよび社内方針に合わせて実施する。

---

## 6. 作業ディレクトリの準備

WSL2 Linux 側に管理用ディレクトリを作成する。

```bash name=prepare-gitea-dir.sh
mkdir -p ~/srv/gitea
cd ~/srv/gitea
```

推奨構成例:

```text
~/srv/gitea/
  docker-compose.yml
  data/
  config/
  backup/
```

---

## 7. Gitea の compose ファイル作成

例:

```yaml name=docker-compose.yml
services:
  gitea:
    image: gitea/gitea:latest
    container_name: gitea
    restart: always
    ports:
      - "3000:3000"
      - "222:22"
    volumes:
      - ./data:/data
      - ./config:/etc/gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__server__ROOT_URL=http://localhost:3000/
```

### 補足

- `3000` は Web UI 用
- `222` は SSH 接続用の例
- 実際のポートは社内ルールに合わせて変更する
- 初期段階ではシンプルな構成でよい

---

## 8. Gitea の起動

WSL2 Linux 側で以下を実行する。

```bash name=start-gitea.sh
docker compose up -d
```

確認:

```bash name=check-gitea.sh
docker compose ps
docker logs gitea
```

---

## 9. 初期設定

Windows 側ブラウザから以下へアクセスする。

```text
http://localhost:3000
```

初期設定では以下を実施する。

- 管理者ユーザー作成
- 基本 URL 設定確認
- リポジトリ作成
- 動作確認用の push / pull 実施

---

## 10. 永続化とバックアップ

### 10.1 永続化対象

- `data/`
- `config/`
- `docker-compose.yml`

### 10.2 バックアップ対象

- Gitea データ
- 設定ファイル
- 利用ポート
- 管理者設定情報
- 運用メモ

---

## 11. 動作確認項目

- Gitea コンテナが起動すること
- Windows 側ブラウザから接続できること
- 管理者ユーザーを作成できること
- リポジトリを作成できること
- clone / push / pull が実施できること

---

## 12. 留意事項

- 初期は 1 台構成であるため、Gitea とビルド環境が同一ホスト上に存在する
- 長期的にはビルド実行環境の分離を前提とする
- WSL2 再起動後の復帰手順を確認しておく
- 社内利用時はポート公開範囲およびアクセス制御を整理する

---

## 13. 次段階

本手順完了後、以下を進める。

- Gitea runner の登録
- Windows runner による自動ビルド確認
- 共有 NuGet フォルダを用いた restore / build 確認
- 再起動後の復帰確認
