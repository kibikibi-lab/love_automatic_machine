# コンポーネント依存関係

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04

---

## 依存関係マトリクス

| 呼び出し元 | 呼び出し先 | 通信方式 | 目的 |
|---|---|---|---|
| S-01 | C-01 | 同期REST | メッセージ生成 |
| S-01 | C-03 | 同期REST | ペルソナ分析 |
| S-01 | C-05 | 同期REST | キャラクター設定取得 |
| S-01 | C-07 | 同期REST | 舐めプ度加算 |
| S-02 | C-02 | 同期REST | 会話ルート生成・返信対応 |
| S-02 | C-06 | 同期REST | 実績記録・レポート生成 |
| S-02 | C-07 | 同期REST | スコア更新 |
| S-03 | C-04 | 同期REST | 認証 |
| S-03 | C-05 | 同期REST | キャラクター設定管理 |
| S-04 | C-07 | 同期REST | スコア・ランキング管理 |
| C-08 | S-01〜S-04 | HTTP REST | 全APIを呼び出す |
| C-09 | 全コンポーネント | CDK定義 | インフラ構成 |

---

## データフロー図

```
ユーザー操作
    |
    v
C-08 (Next.js Frontend)
    |
    | HTTP REST API
    v
API Gateway
    |
    +---> S-01 (MessageOrchestrationService)
    |         |---> C-05 (CharacterSettings)  --> DynamoDB
    |         |---> C-03 (PersonaAnalysis)    --> Bedrock
    |         |---> C-01 (MessageGenerator)   --> Bedrock
    |         +---> C-07 (ScoreRanking)       --> DynamoDB
    |
    +---> S-02 (ConversationService)
    |         |---> C-02 (ConversationRoute)  --> Bedrock
    |         |---> C-06 (ResultsTracking)    --> DynamoDB + Bedrock
    |         +---> C-07 (ScoreRanking)       --> DynamoDB
    |
    +---> S-03 (UserProfileService)
    |         |---> C-04 (UserAuth)           --> Cognito (モック)
    |         +---> C-05 (CharacterSettings)  --> DynamoDB
    |
    +---> S-04 (RankingService)
              +---> C-07 (ScoreRanking)       --> DynamoDB
```

---

## DynamoDBテーブル設計（概要）

| テーブル名 | パーティションキー | ソートキー | 主な属性 |
|---|---|---|---|
| Users | userId | - | profile, createdAt |
| CharacterSettings | userId | - | tone, hobbies, job, successPatterns |
| Conversations | conversationId | userId | messages, goal, status |
| Results | userId | conversationId | replied, met, dropOffStep, report |
| Scores | userId | - | namedProScore, nominationCount |
| Messages | messageId | userId | content, copyCount |

---

## 外部サービス依存

| サービス | 用途 | コンポーネント |
|---|---|---|
| Amazon Bedrock (Claude 3 Sonnet) | メッセージ生成・ペルソナ分析・レポート生成 | C-01・C-03・C-06 |
| Amazon DynamoDB | 全データ永続化 | C-05・C-06・C-07 |
| Amazon Cognito | 認証（将来対応・現在はモック） | C-04 |
| AWS Lambda | 全バックエンド処理 | C-01〜C-07 |
| Amazon API Gateway | REST APIエンドポイント | 全Lambda |
| AWS Amplify | フロントエンドホスティング | C-08 |
| AWS CDK | インフラ定義 | C-09 |
