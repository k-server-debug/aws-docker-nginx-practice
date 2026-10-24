# AWS Docker Nginx Portfolio

## 1. プロジェクト概要

DockerとNginxを使用して静的なWebページを配信するプロジェクトです。

ローカル環境のDocker Desktopで動作確認を行った後、
AWS EC2上にデプロイし、外部ブラウザから
Webページにアクセスできることを確認しました。

Linuxサーバーの構築、Dockerによるコンテナ実行、
ポートマッピング、Webサーバーの動作確認を通じて、
インフラエンジニアに必要な基礎技術を学習しています。

## 2. プロジェクトの目的

- Dockerによるコンテナ環境の構築
- Dockerfileを使用したイメージの作成
- Docker Composeによるコンテナ管理
- Nginxを利用したWebページの配信
- ポートマッピングの理解
- サーバーの状態確認とログ調査

## 3. 使用技術

| 技術 | 用途 |
|---|---|
| AWS EC2 | クラウド上のサーバー環境 |
| Amazon Linux 2023 | Linuxサーバー環境 |
| Docker | コンテナの実行環境 |
| Docker Compose | コンテナの起動・管理 |
| Nginx | Webサーバー |
| HTML / CSS | Webページの作成 |
| Git / GitHub | ソースコードの管理 |

## 4. システム構成

### ローカル環境

```text
Web Browser
     |
     | HTTP :8000
     v
Docker Desktop
     |
     | Port Mapping :8000 -> :80
     v
Nginx Container
     |
     v
index.html
```

### AWS EC2環境

```text
Web Browser
     |
     | HTTP Request (TCP :80)
     v
Internet
     |
     v
AWS EC2 Security Group
     |
     | Inbound TCP :80
     v
EC2 Instance
(Amazon Linux 2023)
     |
     | Docker Port Mapping :80 -> :80
     v
Nginx Container
     |
     v
index.html
```

ローカル環境ではホストの8000番ポートを使用し、
AWS EC2環境では80番ポートを使用しています。

## 5. ファイル構成

```text
aws-docker-nginx-practice/
├── Dockerfile
├── compose.yaml
├── index.html
├── favicon.svg
├── .dockerignore
└── README.md
```

## 6. Dockerfile

Dockerfileを使用してNginxのイメージを作成します。

```dockerfile
FROM nginx

COPY index.html /usr/share/nginx/html/index.html
COPY favicon.svg /usr/share/nginx/html/favicon.svg
```
`index.html`と`favicon.svg`をNginxのドキュメントルートに配置し、
Webページとファビコンを配信できるようにしています。

## 7. Docker Compose

Docker Composeを使用してコンテナを起動します。

```yaml
services:
  web:
    build: .
    ports:
      - "8000:80"
    restart: unless-stopped
```

- `build: .`：同じディレクトリのDockerfileからイメージを作成
- `8000:80`：ホストの8000番ポートをコンテナの80番ポートに接続
- `restart: unless-stopped`：明示的に停止しない限り再起動を試みる

## 8. 起動・動作確認

以下のコマンドは、ローカル環境（Docker Desktop）で
Docker Composeを利用して実行する手順です。

AWS EC2環境では、Dockerイメージをビルドし、
docker runコマンドでコンテナを起動しました。

### コンテナの起動

```bash
docker compose up --build -d
```

### コンテナの状態確認

```bash
docker compose ps
```

### HTTPレスポンスの確認

```bash
curl -I http://localhost:8000
```

### ログの確認

```bash
docker compose logs
```

### コンテナの停止

```bash
docker compose down
```

### ローカル環境での検証結果

検証日：2026年10月24日

#### 実行環境
- OS：Windows
- コンテナ実行環境：Docker Desktop
- Linux実行基盤：WSL 2
- Webサーバー：Nginx

#### 検証内容と結果

| 検証項目 | 結果 |
|---|---|
| Dockerコンテナの起動 | 正常に起動 |
| ポートマッピング | ホスト8000番 → コンテナ80番 |
| HTTPレスポンス | HTTP 200 OK |
| ブラウザからのアクセス | 正常に表示 |
| Nginxの起動ログ | 正常起動を確認 |
| ファビコンの表示 | SVGアイコンの表示を確認 |

#### トラブルシューティング：ファビコンの404エラー

**発生した問題**

ブラウザからアクセスした際、Nginxのログに
`/usr/share/nginx/html/favicon.ico` が存在しないという
エラーが記録されました。

**原因**

Webサイトのアイコンファイルが存在せず、
ブラウザからのアイコン要求に応答できていませんでした。

**対応**

1. `favicon.svg` を作成
2. `index.html` にファビコンの参照先を設定
3. Dockerfileにファイルのコピー処理を追加
4. Dockerイメージを再ビルド
5. ブラウザでアイコンの表示を確認

**学習したこと**

Webページ本体が正常に表示されていても、
関連ファイルの不足によって404エラーが発生することがあります。
ログを確認して原因を特定し、設定とファイル構成を修正することが
重要だと学びました。

### AWS EC2環境でのデプロイ結果

#### 実行環境

- クラウド：AWS EC2
- リージョン：Asia Pacific (Osaka)
- OS：Amazon Linux 2023
- コンテナ実行環境：Docker
- Webサーバー：Nginx
- コンテナイメージ：aws-docker-nginx-portfolio

#### 実施した作業

1. AWS EC2インスタンスの作成
2. Amazon Linux 2023環境の準備
3. DockerとGitのインストール
4. GitHubリポジトリのクローン
5. Dockerイメージのビルド
6. Nginxコンテナの起動
7. HTTPポート80を利用した外部アクセスの確認

#### 検証結果

| 検証項目 | 結果 |
|---|---|
| Dockerイメージのビルド | 成功 |
| Nginxコンテナの起動 | 成功 |
| EC2のパブリックIPからのアクセス | 成功 |
| ブラウザでのWebページ表示 | 成功 |

#### デプロイ時に使用したコマンド

```bash
sudo docker build -t aws-docker-nginx-portfolio .
```

```bash
sudo docker run -d \
  --name portfolio-nginx \
  -p 80:80 \
  --restart unless-stopped \
  aws-docker-nginx-portfolio
```

Dockerイメージを作成し、Nginxコンテナを起動しました。
その後、EC2のパブリックIPアドレスを使用して
外部ブラウザからWebページにアクセスできることを確認しました。

## 9. セキュリティ上の考慮事項

- SSH接続用の秘密鍵をGitHubにアップロードしない
- AWSのアクセスキーやパスワードをソースコードに記載しない
- Security Groupで不要なポートを公開しない
- SSH接続元のIPアドレスを必要に応じて制限する
- 公開前に設定ファイルやGitの履歴に機密情報がないか確認する

## 10. 今後の改善予定

- Dockerコンテナのログ調査手順の整備
- AWS Security Groupの設定見直し
- HTTPS対応の検討
- AWS利用料金の管理
- システム構成図と実際の設定の継続的な整合性確認

## 11. 学習成果

本プロジェクトを通じて、Dockerによるコンテナ実行、
Docker Composeによる環境管理、NginxによるWeb配信、
ポートマッピング、Linuxサーバーの基本操作について理解を深めます。
