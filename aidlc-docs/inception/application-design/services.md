# サービス定義

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04

---

## サービス一覧

| ID | サービス名 | 目的 | 呼び出すコンポーネント |
|---|---|---|---|
| S-01 | MessageOrchestrationService | メッセージ生成の全体フローを制御 | C-01・C-03・C-05・C-07 |
| S-02 | ConversationService | 会話ルート設計・返信対応を制御 | C-02・C-06・C-07 |
| S-03 | UserProfileService | ユーザー情報・キャラクター設定を管理 | C-04・C-05 |
| S-04 | RankingService | スコア・ランキングを管理 | C-07 |

---

## S-01: MessageOrchestrationService

**目的**: 最初のメッセージ生成の全体フローをオーケストレーションする

**フロー**:
```
1. C-05からキャラクター設定を取得
2. C-03でペルソナ分析を実行（任意）
3. C-01でメッセージを生成
4. C-07に舐めプ度（軸1）の加算を通知
5. 生成されたメッセージを返却
```

**APIエンドポイント**: `POST /api/messages/generate`

**入力**:
```json
{
  "userId": "string",
  "targetInfo": {
    "age": "number",
    "hobbyTags": ["string"],
    "profileText": "string (optional)"
  },
  "goal": "phone | dinner"
}
```

**出力**:
```json
{
  "messages": [
    { "content": "string", "reason": "string" }
  ],
  "personaAnalysis": { ... },
  "namedProScore": "number"
}
```

---

## S-02: ConversationService

**目的**: 会話ルート設計・返信対応・実績記録を制御する

**フロー（ルート生成）**:
```
1. C-02で会話ルートを生成
2. 生成されたルートを返却
```

**フロー（返信対応）**:
```
1. C-02で次のメッセージを再生成
2. 離脱ポイントを記録
3. C-07に舐めプ度（軸1）の加算を通知
4. 次のメッセージを返却
```

**フロー（会話終了）**:
```
1. C-06に実績を記録
2. C-06で振り返りレポートを生成
3. C-07にスコアを更新
4. レポートを返却
```

**APIエンドポイント**:
- `POST /api/conversations/routes` — ルート生成
- `POST /api/conversations/reply` — 返信対応
- `POST /api/conversations/finish` — 会話終了・レポート生成

---

## S-03: UserProfileService

**目的**: ユーザー認証・キャラクター設定を管理する

**フロー**:
```
1. C-04でモック認証を実行
2. C-05でキャラクター設定を取得・保存
```

**APIエンドポイント**:
- `POST /api/auth/login` — ログイン（モック）
- `GET /api/users/{userId}/character` — キャラクター設定取得
- `PUT /api/users/{userId}/character` — キャラクター設定更新

---

## S-04: RankingService

**目的**: 舐めプスコア・指名ランキングを管理する

**フロー（コピー通知）**:
```
1. メッセージがコピーされたことを検知
2. C-07に指名数（軸2）の加算を通知
3. コピー元ユーザーに通知
```

**APIエンドポイント**:
- `GET /api/ranking/{type}` — ランキング取得
- `GET /api/users/{userId}/score` — 自分のスコア取得
- `POST /api/messages/{messageId}/copy` — メッセージコピー通知
