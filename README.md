# mirakc + recisdb + EPGStation + NVENC Docker 環境

軽量な **mirakc** と **recisdb** に **EPGStation** を Docker でまとめた録画環境です。
既存の軽量構成が見つけにくかったため、mirakc / recisdb / EPGStation を Github から取得し、
Docker Compose で動作するように構築しました。

EPGStationのエンコードでnvenc CUDAを使う方法は、使っているグラボやDriver、NVIDIA Container Toolkitのバージョンによって動かない場合もありますので、その場合は、通常版のEPGStationを再ビルドしてご使用ください。

**【事前準備】**
事前に **各種ドライバ・Docker・Docker Compose** をインストールしておいてください。
以下の構成が動作する環境であれば準備は概ね完了しています。

🔗 https://github.com/l3tnun/docker-mirakurun-epgstation

**【2026/03追加：EPGStationにnvenc、CUDA追加】**
EPGStationでのエンコードする時にNVIDIAのグラボが使えるようにNVENC対応するための、Dockerfileとエンコードスクリプト設定を追加いたしました。上記【事前準備】と合わせて、ホスト側にNVIDIA DriverとNVIDIA Container Toolkitをインストールしてください。DockerでNVIDIAのグラボを使うのに必要になります。

ChatGPTによる参考：https://chatgpt.com/s/t_69ba59602118819196493d3c0bdba03a
**※ご自分の環境にあわせて設定してください。**

## 📌 動作確認済み環境

- OS: **Ubuntu 24.04.3 LTS**
- Docker / Docker Compose
- チューナー: **PX-Q3PE5**
- カードリーダー: **SCR3310/v2.0**
- グラフィックボード:**GTX 1050 TI**
- ドライバ:**nvidia-driver-570**
- NVIDIA Container Toolkit **1.18.2**
- FFmpeg **6.1.4(Docker内)**
- NVIDIA-SMI **570.211.01**
- Driver Version: **570.211.01**
- CUDA Version: **12.8
  ※上記以外の環境で動作確認していません。**

## 📌 ダウンロード

```bash
git clone -b variant/nvenc https://github.com/lnnllxxl/docker-mirakc-recisdb-epgstation
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
cp epgstation/config/enc_nvenc.js.template epgstation/config/enc_nvenc.js
cp epgstation/config/enc_nvenc-cuda.js.template epgstation/config/enc_nvenc-cuda.js.template
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

起動する前にrecisdをインストールしたmirakcと
nvencを使うepgstationをビルドしてください。
（ビルドすることでmirakcイメージにrecisdとPCカードのツールがインスールされます。）

```
docker compose build mirakc

docker compose build epgstation
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
- **[EPGStation-nvenc-docker](https://github.com/kazuki0824/EPGStation-nvenc-docker)**
  https://github.com/kazuki0824/EPGStation-nvenc-docker

## 📌 最後に

掲載してる内容に問題がある。
不備があるなどしましたらご連絡ください。📄

X:@lx_xlln_nl

## License

本リポジトリは **MIT License** に基づいて公開されています。
