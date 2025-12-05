# mirakc + recisdb + EPGStation Docker 環境

軽量な **mirakc** と **recisdb** に **EPGStation** を Docker でまとめた録画環境です。
既存の軽量構成が見つけにくかったため、mirakc / recisdb / EPGStation を Github から取得し、
Docker Compose で動作するように構築しました。

事前に **各種ドライバ・Docker・Docker Compose** をインストールしておいてください。
以下の構成が動作する環境であれば準備は概ね完了しています。

🔗 https://github.com/l3tnun/docker-mirakurun-epgstation

## 📌 動作確認済み環境

- OS: **Ubuntu 24.04.3 LTS**
- Docker / Docker Compose
- チューナー: **PX-Q3PE5**
- カードリーダー: **SCR3310/v2.0**

## 📌 ダウンロード

```bash
git clone https://github.com/lnnllxxl/docker-mirakc-recisdb-epgstation
cd docker-mirakc-recisdb-epgstation
```

## 📌 ファイルの準備

サンプルファイルをコピーし、実際の設定ファイルとして使用します。

### ● Docker

```bash
cp docker-compose.sample.yml docker-compose.yml
```

### ● mirakc

```bash
cp mirakc/config.sample.yml mirakc/config.yml
```

### ● EPGStation

```bash
cp epgstation/config/config.yml.template epgstation/config/config.yml
cp epgstation/config/enc.js.template epgstation/config/enc.js
cp epgstation/config/epgUpdaterLogConfig.sample.yml epgstation/config/epgUpdaterLogConfig.yml
cp epgstation/config/operatorLogConfig.sample.yml epgstation/config/operatorLogConfig.yml
cp epgstation/config/serviceLogConfig.sample.yml epgstation/config/serviceLogConfig.yml
```

## 📌 設定方法

### 1. docker-compose.yml の設定

以下を自身の環境に合わせて編集してください。

- **devices:**→ 使用するチューナーデバイスのパス（例: `/dev/px4video0`）
- **volumes:**
  → 録画ファイルの保存先ディレクトリ（内蔵HDD など）

### 2. mirakc 設定（config.yml）

主に次の部分を編集します。

- **ISDBScanner で生成したチャンネルスキャン結果を貼り付け**
- `channels:` の設定
- `tuners:` にチューナーデバイスパスを記述

※recisdb は mirakc コンテナ内で動作するため、**decode-filter の設定は不要** です。

### 3. EPGStation 設定

ポート番号などを変更していなければ、基本的にテンプレートのままで動作します。

## build

起動する前にrecisdをインストールしたmirakcをビルドしてください。
（ビルドすることでmirakcイメージにrecisdとPCカードのツールがインスールされます。）

```
docker compose build mirakc
```

## 📌 起動

```bash
docker compose up -d
```

## 📌 動作確認

### ● mirakc の確認

以下へアクセスしてレスポンスがあれば OK。

```
http://<サーバーIP>:40772/api/version
```

番組情報が揃うまで少し時間がかかります。

### ● miraview の確認

miraviewコンテナも含めていますので、miraviewからも確認出来ると思います。

```
http://<サーバーIP>:40772/miraview/index.html
```

### ● EPGStation の確認

ブラウザで以下にアクセス:

```
http://<サーバーIP>:8888
```

録画一覧や番組表が表示されれば成功です。

## 📌 参考リンク

- docker-mirakurun-epgstation
  https://github.com/l3tnun/docker-mirakurun-epgstation/
- docker-mirakc-epgstation
  https://github.com/5ym/docker-mirakc-epgstation/
- mirakc（できる mirakc）
  https://mirakc.github.io/dekiru-mirakc/stable/
  https://github.com/mirakc/mirakc
- miraview
  https://github.com/maeda577/miraview
- recisdb-rs
  https://github.com/kazuki0824/recisdb-rs
- mirakc コンテナへ recisdb-rs を統合https://www.kreuzen.org/2025/03/18/2025%E5%B9%B4%E7%89%88-mirack%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB/
- ISDBScanner
  https://github.com/tsukumijima/ISDBScanner

## 📌 最後に

掲載してる内容に問題がある。
不備があるなどしましたらご連絡ください。📄

X:@lx_xlln_nl

## License

本リポジトリは **MIT License** に基づいて公開されています。
