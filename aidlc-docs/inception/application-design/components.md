# コンポーネント定義

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04

---

## コンポーネント一覧

| ID | コンポーネント名 | 種別 | 対応Unit |
|---|---|---|---|
| C-01 | MessageGeneratorComponent | Lambda関数 | Unit 1 |
| C-02 | ConversationRouteComponent | Lambda関数 | Unit 2 |
| C-03 | PersonaAnalysisComponent | Lambda関数 | Unit 3 |
| C-04 | UserAuthComponent | Lambda関数 + Cognito | Unit 4 |
| C-05 | CharacterSettingsComponent | Lambda関数 | Unit 5 |
| C-06 | ResultsTrackingComponent | Lambda関数 | Unit 6 |
| C-07 | ScoreRankingComponent | Lambda関数 | Unit 7 |
| C-08 | FrontendComponent | Next.js | Unit 8 |
| C-09 | InfrastructureComponent | AWS CDK | Unit 9 |

---

## C-01: MessageGeneratorComponent

**目的**: Amazon Bedrockを使って最初のメッセージを生成する

**責務**:
- キャラクター設定・ペルソナ分析結果・ゴールを受け取りプロンプトを構築する
- Claude 3 Sonnetを呼び出してメッセージを生成する
- 複数パターン（3件以上）を生成して返却する
- 舐めプスコア（軸1）の加算をScoreRankingComponentに通知する

**インターフェース**:
- 入力: `{ characterSettings, personaAnalysis, goal, targetInfo }`
- 出力: `{ messages: [{ content, reason }], scoreUpdated: boolean }`

---

## C-02: ConversationRouteComponent

**目的**: ゴールまでの会話シナリオを設計し、返信対応を行う

**責務**:
- 最初のメッセージからゴールまでの複数ルートを生成する
- 実際の返信を受け取り次のメッセージを再生成する
- 離脱ポイント（返信が止まったステップ）を記録する
- ゴール変更にも対応する

**インターフェース**:
- 入力（ルート生成）: `{ firstMessage, personaAnalysis, goal }`
- 出力（ルート生成）: `{ routes: [{ steps, difficulty, estimatedTurns }] }`
- 入力（練り直し）: `{ conversationHistory, latestReply, goal }`
- 出力（練り直し）: `{ nextMessage, dropOffPoint }`

---

## C-03: PersonaAnalysisComponent

**目的**: 相手のプロフィール情報からペルソナを分析する

**責務**:
- 趣味タグ・年齢・プロフィール文（任意）を受け取り分析する
- Claude 3 Sonnetで性格傾向・価値観・刺さるアプローチを推定する
- 分析結果をMessageGeneratorComponentとConversationRouteComponentに提供する
- 入力が最小限（タグのみ）でも動作する

**インターフェース**:
- 入力: `{ age, hobbyTags: string[], profileText?: string }`
- 出力: `{ personality, values, communicationStyle, recommendedApproach }`

---

## C-04: UserAuthComponent

**目的**: ユーザー認証を管理する（プロトタイプ版はモック）

**責務**:
- プロトタイプ版: 固定ユーザーIDでモック認証を行う
- ユーザーIDをセッションに保持する
- 将来的にCognitoへの切り替えを想定した設計にする

**インターフェース**:
- 入力: `{ mockUserId?: string }`
- 出力: `{ userId, sessionToken }`

---

## C-05: CharacterSettingsComponent

**目的**: ユーザーのキャラクター設定を管理する

**責務**:
- キャラクター設定の登録・更新・取得を行う
- 話し方トーン・趣味・職業・成功パターンを保存する
- DynamoDBのキャラクターテーブルを操作する

**インターフェース**:
- 入力（登録/更新）: `{ userId, tone, hobbies, job, successPatterns }`
- 出力（取得）: `{ characterSettings }`

---

## C-06: ResultsTrackingComponent

**目的**: 会話の実績を記録し振り返りレポートを生成する

**責務**:
- 返信率・離脱ポイント・会えた実績を記録する
- ゲーミフィケーション（ポイント・バッジ）を付与する
- Claude 3 Sonnetで振り返りレポートを自動生成する
- DynamoDBの実績テーブルを操作する

**インターフェース**:
- 入力（記録）: `{ userId, conversationId, result, dropOffStep }`
- 出力（レポート）: `{ replyRate, goalAchieved, improvements, nextStrategy }`

---

## C-07: ScoreRankingComponent

**目的**: 舐めプスコアを管理し指名ランキングを提供する

**責務**:
- 軸1（舐めプ度）: AIを使うたびに加算する
- 軸2（指名数）: メッセージがコピーされるたびに加算する
- 匿名化されたランキングデータを集計・返却する
- DynamoDBのスコアテーブルを操作する

**インターフェース**:
- 入力（スコア更新）: `{ userId, axis: 'namedPro' | 'nomination', delta: number }`
- 出力（ランキング）: `{ nominationRanking, namedProRanking, userRank }`

---

## C-08: FrontendComponent

**目的**: ユーザーインターフェースを提供する

**責務**:
- Next.js（TypeScript）でWebアプリを構築する
- 各バックエンドAPIを呼び出してデータを表示する
- 「舐めプしていく」演出UIを実装する
- AWS Amplifyでホスティングする

**主要画面**:
- オンボーディング（AI依存度診断）
- キャラクター設定
- 相手情報入力・ペルソナ分析
- メッセージ生成・会話ルート表示
- 実績記録・振り返りレポート
- 舐めプスコア・指名ランキング

---

## C-09: InfrastructureComponent

**目的**: AWSインフラをコードで管理する

**責務**:
- AWS CDK（TypeScript）でインフラを定義する
- Lambda・API Gateway・DynamoDB・Cognito・Amplifyを構成する
- 環境変数・シークレット管理を行う
