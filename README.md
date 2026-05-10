# env-gitea-plan

Gitea を用いたソース管理基盤と、Windows ビルド実行環境の初期導入を整理するための文書リポジトリです。

本リポジトリでは、古い Windows アプリ開発を前提とし、以下の方針を扱います。

- ソース管理は Gitea を採用する
- Gitea は Windows 上の WSL2 Linux 環境で運用する
- コンテナ実行には Linux 側 Engine を用いる
- Docker Desktop は今回は採用しない
- ビルドは Windows 側で実施する
- NuGet は共有フォルダ運用とする
- 初期は 1 台構成で実証検証を行う
- 将来的にビルド実行環境を分離する

---

## 文書一覧

- [`docs/gitea-build-plan.md`](docs/gitea-build-plan.md)
  - 全体方針の整理
- [`docs/verification-plan.md`](docs/verification-plan.md)
  - 実証検証の実施計画
- [`docs/windows-runner-setup.md`](docs/windows-runner-setup.md)
  - Windows runner の導入手順
- [`docs/nuget-share-operation.md`](docs/nuget-share-operation.md)
  - NuGet 共有フォルダ運用手順
- [`docs/wsl2-gitea-setup.md`](docs/wsl2-gitea-setup.md)
  - WSL2 + Gitea 構築手順

---

## 読む順番

1. `docs/gitea-build-plan.md`
2. `docs/verification-plan.md`
3. `docs/wsl2-gitea-setup.md`
4. `docs/windows-runner-setup.md`
5. `docs/nuget-share-operation.md`

---

## 文書の役割

### 1. 方針確認

まず `docs/gitea-build-plan.md` で、導入方針と構成前提を確認します。

### 2. 実証検証の整理

次に `docs/verification-plan.md` で、何を検証し、どの条件で成立と判断するかを確認します。

### 3. 構築手順の確認

その後、以下の順に個別手順を確認します。

- `docs/wsl2-gitea-setup.md`
- `docs/windows-runner-setup.md`
- `docs/nuget-share-operation.md`

---

## 想定する初期構成

```text
+------------------------------------------------------+
| Windows Host                                         |
|------------------------------------------------------|
| [Windows 側]                                         |
| - ビルド実行環境                                     |
| - Visual Studio Build Tools / MSBuild                |
| - NuGet                                              |
| - Gitea runner                                       |
| - 共有 NuGet フォルダへのアクセス                    |
|                                                      |
| [WSL2 Linux 側]                                      |
| - Gitea                                              |
| - Linux 側 Engine によるコンテナ実行環境            |
+------------------------------------------------------+
```

---

## 今後追加するとよい文書

今後、必要に応じて以下の文書を追加できます。

- Gitea runner 用 workflow サンプル
- 障害時の復旧手順
- バックアップ運用方針
- ビルド成果物保管方針
- 将来のビルド実行環境分離計画

---

## 補足

本リポジトリの文書は、初期導入および実証検証を主目的としており、本番向け高可用性構成や大規模運用を前提としたものではありません。
