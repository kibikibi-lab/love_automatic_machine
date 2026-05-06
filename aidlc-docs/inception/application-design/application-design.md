# アプリケーション設計書

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04

---

## 1. 設計方針

| 項目 | 決定内容 | 理由 |
|---|---|---|
| APIスタイル | REST API | シンプルで標準的。クライアント・サーバー間の契約が明確になる |
| AIモデル | Amazon Bedrock / Claude 3 Sonnet | 日本語品質・速度バランス最良 |
| Unit間通信 | 同期REST（直接API呼び出し） | シンプル・即時レスポンス |
| 認証 | モック（固定ユーザーID） | プロトタイプフェーズでは認証より機能検証を優先する |

---

## 2. アーキテクチャ概要

```
+--------------------------------------------------+
|  C-08: Frontend (Next.js / AWS Amplify)          |
+--------------------------------------------------+
                      |
                      | HTTP REST
                      v
+--------------------------------------------------+
|  API Gateway                                     |
+--------------------------------------------------+
        |           |           |           |
        v           v           v           v
   S-01          S-02        S-03        S-04
MessageOrch  Conversation  UserProfile  Ranking
Service      Service       Service      Service
        |           |           |
        v           v           v
+-------+     +-------+   +-------+
| C-01  |     | C-02  |   | C-04  |
| Msg   |     | Route |   | Auth  |
| Gen   |     | Engine|   |(Mock) |
+-------+     +-------+   +-------+
| C-03  |     | C-06  |   | C-05  |
| Persona|    | Results|  | Char  |
| Anal  |     | Track |   | Sett  |
+-------+     +-------+   +-------+
        |           |           |
        +-----+-----+-----------+
              |
              v
+--------------------------------------------------+
|  C-07: ScoreRankingComponent                     |
+--------------------------------------------------+
              |
              v
+--------------------------------------------------+
|  Amazon DynamoDB / Amazon Bedrock / Cognito      |
+--------------------------------------------------+
```

---

## 3. コンポーネント一覧

| ID | コンポーネント | 種別 | 対応Unit |
|---|---|---|---|
| C-01 | MessageGeneratorComponent | Lambda | Unit 1 |
| C-02 | ConversationRouteComponent | Lambda | Unit 2 |
| C-03 | PersonaAnalysisComponent | Lambda | Unit 3 |
| C-04 | UserAuthComponent | Lambda + Cognito(Mock) | Unit 4 |
| C-05 | CharacterSettingsComponent | Lambda | Unit 5 |
| C-06 | ResultsTrackingComponent | Lambda | Unit 6 |
| C-07 | ScoreRankingComponent | Lambda | Unit 7 |
| C-08 | FrontendComponent | Next.js | Unit 8 |
| C-09 | InfrastructureComponent | AWS CDK | Unit 9 |

詳細: `components.md` / `component-methods.md` 参照

---

## 4. サービス一覧

| ID | サービス | 主要APIエンドポイント |
|---|---|---|
| S-01 | MessageOrchestrationService | `POST /api/messages/generate` |
| S-02 | ConversationService | `POST /api/conversations/routes` `POST /api/conversations/reply` `POST /api/conversations/finish` |
| S-03 | UserProfileService | `POST /api/auth/login` `GET/PUT /api/users/{userId}/character` |
| S-04 | RankingService | `GET /api/ranking/{type}` `GET /api/users/{userId}/score` `POST /api/messages/{messageId}/copy` |

詳細: `services.md` 参照

---

## 5. データフロー（主要シナリオ）

### シナリオ1: 最初のメッセージ生成

```
ユーザー → C-08 → S-01
  → C-05（キャラクター設定取得）
  → C-03（ペルソナ分析・任意）
  → C-01（Bedrockでメッセージ生成）
  → C-07（舐めプ度+1）
  → C-08（3パターン表示）
```

### シナリオ2: 返信が来て練り直し

```
ユーザー → C-08 → S-02
  → C-02（返信を受けて次のメッセージ再生成）
  → C-07（舐めプ度+1）
  → C-08（次のメッセージ表示）
```

### シナリオ3: 会話終了・レポート生成

```
ユーザー → C-08 → S-02
  → C-06（実績記録）
  → C-06（Bedrockでレポート生成）
  → C-07（スコア更新）
  → C-08（レポート表示）
```

### シナリオ4: メッセージコピー → 指名数加算

```
ユーザーB → C-08（コピーボタン押下）→ S-04
  → C-07（ユーザーAの指名数+1）
  → ユーザーAに通知
```

---

## 6. 依存関係・外部サービス

詳細: `component-dependency.md` 参照

| 外部サービス | 用途 |
|---|---|
| Amazon Bedrock (Claude 3 Sonnet) | メッセージ生成・ペルソナ分析・レポート生成 |
| Amazon DynamoDB | 全データ永続化 |
| Amazon Cognito | 認証（将来対応・現在はモック） |
| AWS Lambda + API Gateway | バックエンド処理 |
| AWS Amplify | フロントエンドホスティング |
| AWS CDK | インフラ定義 |

---

## 7. 変更履歴

| 日付 | バージョン | 内容 |
|---|---|---|
| 2026-05-04 | v1 | 初版作成 |
