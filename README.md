# AWS Docker Nginx Portfolio

## 1. プロジェクト概要

DockerとNginxを使用してWebサーバー環境を構築し、
静的なWebページを配信する学習プロジェクトです。

AWS EC2上での実行を想定し、Linux、Docker、
Docker Composeを利用したサーバー構築の基礎を学習します。

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

※ 実際に使用・検証した技術は、作業完了後に確定します。

## 4. システム構成

```text
Web Browser
     |
     | HTTP :8000
     v
AWS EC2
     |
     | Security Group
     v
Docker Container
     |
     | Port Mapping :8000 -> :80
     v
Nginx
     |
     v
index.html
```

※ 上記は想定構成です。EC2への再デプロイ後に実際の構成を確認します。

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
```

`index.html`をNginxのドキュメントルートに配置し、
コンテナ起動後にWebページを配信できるようにします。

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

Dockerがインストールされた環境で実行します。

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

検証日：2026年10月22日

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

※ 現時点ではローカル環境での検証結果です。
AWS EC2へのデプロイと動作確認は今後実施します。

## 9. セキュリティ上の考慮事項

- SSH接続用の秘密鍵をGitHubにアップロードしない
- AWSのアクセスキーやパスワードをソースコードに記載しない
- Security Groupで不要なポートを公開しない
- SSH接続元のIPアドレスを必要に応じて制限する
- 公開前に設定ファイルやGitの履歴に機密情報がないか確認する

## 10. 今後の改善予定

- AWS EC2環境への再デプロイ
- HTTPアクセスとコンテナの動作確認
- Nginxのアクセスログ・エラーログの調査
- トラブルシューティング記録の追加
- READMEと実際のシステム構成の整合性確認

## 11. 学習成果

本プロジェクトを通じて、Dockerによるコンテナ実行、
Docker Composeによる環境管理、NginxによるWeb配信、
ポートマッピング、Linuxサーバーの基本操作について理解を深めます。
