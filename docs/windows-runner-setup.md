# Windows runner 導入手順

## 1. 目的

Gitea 上で管理するソースコードについて、Windows 環境で自動ビルドを実行できるようにするため、Windows runner を導入する。

本手順は、初期の 1 台構成を前提とし、将来的にビルド実行環境を別マシンへ分離することを見据えた内容とする。

---

## 2. 前提条件

- Gitea が利用可能であること
- Windows マシンを runner 用に利用できること
- Gitea に接続可能であること
- 共有 NuGet フォルダにアクセス可能であること
- 管理者権限でソフトウェア導入ができること

---

## 3. Windows 側に用意するもの

### 必須

- Git
- NuGet.exe
- Visual Studio Build Tools または Visual Studio
- MSBuild
- 必要な .NET Framework targeting pack

### 必要に応じて

- .NET SDK
- Windows SDK
- vstest
- 署名ツール
- インストーラ作成ツール

---

## 4. 推奨ディレクトリ構成

```text
C:\
  runner\
    bin\
    work\
    logs\
  Tools\
    nuget\
      nuget.exe
D:\
  nuget-cache\
  artifacts\
```

### 役割

- `C:\runner\bin` : runner 本体配置
- `C:\runner\work` : 作業ディレクトリ
- `C:\runner\logs` : ログ保管
- `C:\Tools\nuget` : NuGet 実行ファイル配置
- `D:\nuget-cache` : NuGet キャッシュ
- `D:\artifacts` : ビルド成果物の一時出力

---

## 5. 導入手順

### 5.1 Git の導入

- Git for Windows を導入する
- `git --version` で利用可能であることを確認する

### 5.2 NuGet.exe の配置

- `nuget.exe` を配置する
- 例: `C:\Tools\nuget\nuget.exe`
- 必要に応じて PATH を設定する

### 5.3 Build Tools の導入

- Visual Studio Build Tools または Visual Studio を導入する
- 対象プロジェクトに必要なワークロードを選択する
- `msbuild` が利用できることを確認する

### 5.4 必要な targeting pack の導入

- 対象ソリューションに必要な .NET Framework targeting pack を導入する
- 古いプロジェクト形式では追加コンポーネントが必要になる場合があるため注意する

### 5.5 共有 NuGet フォルダ接続確認

- `\\fileserver\nuget` にアクセス可能であることを確認する
- runner 実行アカウントでも参照できることを確認する

### 5.6 Gitea で runner 登録情報を取得

- Gitea の管理画面または対象範囲の設定画面から runner 登録用トークンを取得する
- runner 名、利用ラベル、接続先 URL を整理する

### 5.7 runner の配置と登録

- runner 実行ファイルを `C:\runner\bin` に配置する
- Gitea の登録情報を用いて runner を登録する
- ラベル例:
  - `windows`
  - `legacy-build`
  - `net48`

### 5.8 runner のサービス化

- runner を Windows サービスとして登録する
- 自動起動設定を行う
- 再起動後も利用できることを確認する

---

## 6. 動作確認

### 6.1 ツール確認

以下を確認する。

- `git --version`
- `nuget help`
- `msbuild -version`

### 6.2 共有フォルダ確認

- `\\fileserver\nuget` にアクセスできること
- 必要なパッケージが見えること

### 6.3 Gitea 側確認

- runner が online 状態になること
- 指定ラベルでジョブを受けられること

### 6.4 ビルド確認

- checkout
- restore
- build

の順でジョブを実行し、成功することを確認する。

---

## 7. 留意事項

- runner 実行アカウントの権限不足に注意する
- 共有 NuGet フォルダは runner 実行アカウントから参照可能である必要がある
- 古い Windows アプリでは Build Tools の追加構成が必要になる場合がある
- 初期は 1 台構成で導入するが、長期的にはビルド実行環境の分離を前提とする

---

## 8. 将来の分離を見据えた考え方

- runner ラベルは役割ベースで付与する
- ディレクトリ構成を固定化する
- NuGet キャッシュや成果物出力先を整理する
- 導入した Build Tools 構成を記録する

これにより、将来的に別 Windows マシンへ移行しやすくなる。
