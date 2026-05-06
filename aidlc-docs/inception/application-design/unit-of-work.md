# Unit定義書

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04  
**リポジトリ構成**: モノレポ  
**ディレクトリ構成**: 機能別（A案）

---

## リポジトリ構成

```
love_automatic_machine/                # リポジトリルート
  ├── frontend/                        # Unit 8: Next.js フロントエンド
  │   ├── src/
  │   │   ├── app/                     # App Router（Next.js 14）
  │   │   ├── components/              # UIコンポーネント
  │   │   ├── hooks/                   # カスタムフック
  │   │   └── lib/                     # APIクライアント・ユーティリティ
  │   ├── package.json
  │   └── tsconfig.json
  │
  ├── backend/                         # Unit 1〜7: Lambda関数群
  │   ├── message/                     # Unit 1: AIメッセージ生成エンジン
  │   │   ├── src/handler.ts
  │   │   ├── src/messageGenerator.ts
  │   │   └── package.json
  │   ├── conversation/                # Unit 2: 会話ルート設計エンジン
  │   │   ├── src/handler.ts
  │   │   ├── src/routeEngine.ts
  │   │   └── package.json
  │   ├── persona/                     # Unit 3: 相手ペルソナ分析エンジン
  │   │   ├── src/handler.ts
  │   │   ├── src/personaAnalyzer.ts
  │   │   └── package.json
  │   ├── auth/                        # Unit 4: ユーザー管理・認証
  │   │   ├── src/handler.ts
  │   │   ├── src/authService.ts
  │   │   └── package.json
  │   ├── character/                   # Unit 5: キャラクター設定管理
  │   │   ├── src/handler.ts
  │   │   ├── src/characterService.ts
  │   │   └── package.json
  │   ├── results/                     # Unit 6: 実績トラッキング・レポート
  │   │   ├── src/handler.ts
  │   │   ├── src/resultsTracker.ts
  │   │   └── package.json
  │   └── score/                       # Unit 7: 舐めプスコア・指名ランキング
  │       ├── src/handler.ts
  │       ├── src/scoreService.ts
  │       └── package.json
  │
  ├── infrastructure/                  # Unit 9: AWS CDK
  │   ├── lib/
  │   │   ├── renai-jidosoji-stack.ts  # メインスタック
  │   │   ├── lambda-stack.ts          # Lambda定義
  │   │   ├── dynamodb-stack.ts        # DynamoDB定義
  │   │   └── api-gateway-stack.ts     # API Gateway定義
  │   ├── bin/app.ts
  │   └── package.json
  │
  ├── aidlc-docs/                      # AIDLCドキュメント
  ├── README.md
  └── package.json                     # ルートpackage.json（ワークスペース管理）
```

---

## Unit一覧

| Unit ID | Unit名 | 種別 | 主要技術 | 対応コンポーネント |
|---|---|---|---|---|
| Unit 1 | AIメッセージ生成エンジン | Lambda | TypeScript / Bedrock | C-01 |
| Unit 2 | 会話ルート設計エンジン | Lambda | TypeScript / Bedrock | C-02 |
| Unit 3 | 相手ペルソナ分析エンジン | Lambda | TypeScript / Bedrock | C-03 |
| Unit 4 | ユーザー管理・認証 | Lambda | TypeScript / Cognito(Mock) | C-04 |
| Unit 5 | キャラクター設定管理 | Lambda | TypeScript / DynamoDB | C-05 |
| Unit 6 | 実績トラッキング・レポート | Lambda | TypeScript / DynamoDB / Bedrock | C-06 |
| Unit 7 | 舐めプスコア・指名ランキング | Lambda | TypeScript / DynamoDB | C-07 |
| Unit 8 | フロントエンド UI | Next.js | TypeScript / React | C-08 |
| Unit 9 | AWSインフラ構成 | CDK | TypeScript / AWS CDK | C-09 |

---

## Unit詳細

### Unit 1: AIメッセージ生成エンジン
- **ディレクトリ**: `backend/message/`
- **責務**: キャラクター設定・ペルソナ分析・ゴールをもとにBedrock（Claude 3 Sonnet）で最初のメッセージを複数パターン生成する
- **APIエンドポイント**: `POST /api/messages/generate`
- **依存Unit**: Unit 3（ペルソナ分析）・Unit 5（キャラクター設定）・Unit 7（スコア更新）
- **外部依存**: Amazon Bedrock（Claude 3 Sonnet）

### Unit 2: 会話ルート設計エンジン
- **ディレクトリ**: `backend/conversation/`
- **責務**: ゴールまでの会話シナリオ生成・返信対応・離脱ポイント記録
- **APIエンドポイント**: `POST /api/conversations/routes` / `POST /api/conversations/reply` / `POST /api/conversations/finish`
- **依存Unit**: Unit 6（実績記録）・Unit 7（スコア更新）
- **外部依存**: Amazon Bedrock（Claude 3 Sonnet）

### Unit 3: 相手ペルソナ分析エンジン
- **ディレクトリ**: `backend/persona/`
- **責務**: 趣味タグ・年齢・プロフィール文（任意）からペルソナを分析する
- **APIエンドポイント**: `POST /api/persona/analyze`
- **依存Unit**: なし（独立）
- **外部依存**: Amazon Bedrock（Claude 3 Sonnet）

### Unit 4: ユーザー管理・認証
- **ディレクトリ**: `backend/auth/`
- **責務**: モック認証・ユーザーID管理（将来Cognito対応）
- **APIエンドポイント**: `POST /api/auth/login`
- **依存Unit**: なし（独立）
- **外部依存**: Amazon Cognito（将来対応・現在はモック）

### Unit 5: キャラクター設定管理
- **ディレクトリ**: `backend/character/`
- **責務**: ユーザーのキャラクター設定（話し方・趣味・成功パターン）の登録・更新・取得
- **APIエンドポイント**: `GET/PUT /api/users/{userId}/character`
- **依存Unit**: Unit 4（ユーザーID）
- **外部依存**: Amazon DynamoDB

### Unit 6: 実績トラッキング・レポート
- **ディレクトリ**: `backend/results/`
- **責務**: 会話結果の記録・ゲーミフィケーション・振り返りレポート自動生成
- **APIエンドポイント**: `POST /api/results` / `GET /api/results/{userId}/report`
- **依存Unit**: Unit 4（ユーザーID）
- **外部依存**: Amazon DynamoDB・Amazon Bedrock（レポート生成）

### Unit 7: 舐めプスコア・指名ランキング
- **ディレクトリ**: `backend/score/`
- **責務**: 舐めプ度（軸1）・指名数（軸2）の管理・匿名ランキング集計
- **APIエンドポイント**: `GET /api/ranking/{type}` / `GET /api/users/{userId}/score` / `POST /api/messages/{messageId}/copy`
- **依存Unit**: Unit 4（ユーザーID）
- **外部依存**: Amazon DynamoDB

### Unit 8: フロントエンド UI
- **ディレクトリ**: `frontend/`
- **責務**: Next.js（TypeScript）でWebアプリを構築。「舐めプしていく」演出UIを実装
- **主要画面**: オンボーディング / キャラクター設定 / メッセージ生成 / 会話ルート / 実績・レポート / スコア・ランキング
- **依存Unit**: Unit 1〜7（全APIを呼び出す）
- **外部依存**: AWS Amplify（ホスティング）

### Unit 9: AWSインフラ構成
- **ディレクトリ**: `infrastructure/`
- **責務**: AWS CDK（TypeScript）でLambda・API Gateway・DynamoDB・Cognito・Amplifyを定義
- **依存Unit**: Unit 1〜8（全Unitのインフラを定義）
- **外部依存**: AWS CDK
