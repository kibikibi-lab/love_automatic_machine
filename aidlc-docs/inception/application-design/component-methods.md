# コンポーネントメソッド定義

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04  
**注意**: 詳細なビジネスロジックはConstruction PhaseのFunctional Designで定義する

---

## C-01: MessageGeneratorComponent

| メソッド名 | 目的 | 入力 | 出力 |
|---|---|---|---|
| `generateFirstMessage` | 最初のメッセージを複数パターン生成 | `GenerateMessageInput` | `GenerateMessageOutput` |
| `buildPrompt` | Bedrock用プロンプトを構築 | `characterSettings, personaAnalysis, goal` | `string` |
| `callBedrock` | Claude 3 Sonnetを呼び出す | `prompt: string` | `string` |
| `parseMessages` | Bedrockの出力を複数パターンに分割 | `rawOutput: string` | `Message[]` |

```typescript
// 型定義
interface GenerateMessageInput {
  characterSettings: CharacterSettings;
  personaAnalysis?: PersonaAnalysis; // 任意
  goal: 'phone' | 'dinner';
  targetInfo: TargetInfo;
}

interface GenerateMessageOutput {
  messages: Array<{
    content: string;
    reason: string;
  }>;
  scoreUpdated: boolean;
}
```

---

## C-02: ConversationRouteComponent

| メソッド名 | 目的 | 入力 | 出力 |
|---|---|---|---|
| `generateRoutes` | ゴールまでの会話ルートを生成 | `RouteGenerationInput` | `RouteGenerationOutput` |
| `regenerateMessage` | 返信を受けて次のメッセージを再生成 | `RegenerateInput` | `RegenerateOutput` |
| `recordDropOff` | 離脱ポイントを記録 | `conversationId, step` | `void` |
| `changeGoal` | ゴールを変更して再設計 | `conversationId, newGoal` | `RouteGenerationOutput` |

```typescript
interface RouteGenerationInput {
  firstMessage: string;
  personaAnalysis?: PersonaAnalysis;
  goal: 'phone' | 'dinner';
}

interface RouteGenerationOutput {
  routes: Array<{
    name: string; // 例: "好反応ルート"
    steps: ConversationStep[];
    difficulty: 'easy' | 'medium' | 'hard';
    estimatedTurns: number;
  }>;
}

interface RegenerateInput {
  conversationHistory: ConversationStep[];
  latestReply: string;
  goal: 'phone' | 'dinner';
}
```

---

## C-03: PersonaAnalysisComponent

| メソッド名 | 目的 | 入力 | 出力 |
|---|---|---|---|
| `analyzePersona` | 相手のペルソナを分析 | `PersonaAnalysisInput` | `PersonaAnalysis` |
| `buildAnalysisPrompt` | 分析用プロンプトを構築 | `age, hobbyTags, profileText?` | `string` |

```typescript
interface PersonaAnalysisInput {
  age: number;
  hobbyTags: string[]; // 1〜3個
  profileText?: string; // 任意
}

interface PersonaAnalysis {
  personality: string;
  values: string;
  communicationStyle: string;
  recommendedApproach: string;
}
```

---

## C-04: UserAuthComponent

| メソッド名 | 目的 | 入力 | 出力 |
|---|---|---|---|
| `mockLogin` | モック認証でユーザーIDを返す | `mockUserId?: string` | `AuthResult` |
| `getCurrentUser` | 現在のユーザー情報を取得 | `sessionToken: string` | `User` |

```typescript
interface AuthResult {
  userId: string;
  sessionToken: string;
}
```

---

## C-05: CharacterSettingsComponent

| メソッド名 | 目的 | 入力 | 出力 |
|---|---|---|---|
| `saveSettings` | キャラクター設定を保存 | `userId, CharacterSettings` | `void` |
| `getSettings` | キャラクター設定を取得 | `userId: string` | `CharacterSettings` |
| `updateSettings` | キャラクター設定を更新 | `userId, Partial<CharacterSettings>` | `CharacterSettings` |

```typescript
interface CharacterSettings {
  tone: 'frank' | 'polite' | 'funny';
  hobbies: string[];
  job: string;
  age: number;
  favoriteDatePlans: string[];
  successPatterns: string[];
}
```

---

## C-06: ResultsTrackingComponent

| メソッド名 | 目的 | 入力 | 出力 |
|---|---|---|---|
| `recordResult` | 会話結果を記録 | `ResultInput` | `RecordOutput` |
| `generateReport` | 振り返りレポートを生成 | `conversationId: string` | `Report` |
| `getHistory` | 過去の実績一覧を取得 | `userId: string` | `Result[]` |

```typescript
interface ResultInput {
  userId: string;
  conversationId: string;
  replied: boolean;
  met: boolean;
  dropOffStep?: number;
}

interface Report {
  replyRate: number;
  goalAchieved: boolean;
  improvements: string[];
  nextStrategy: string;
}
```

---

## C-07: ScoreRankingComponent

| メソッド名 | 目的 | 入力 | 出力 |
|---|---|---|---|
| `addScore` | スコアを加算する | `userId, axis, delta` | `ScoreResult` |
| `getMyScore` | 自分のスコアを取得 | `userId: string` | `UserScore` |
| `getRanking` | ランキングを取得 | `type: RankingType` | `RankingEntry[]` |
| `notifyNomination` | 指名通知を送る | `userId, nominatorCount` | `void` |

```typescript
type RankingType = 'nomination' | 'namedPro' | 'replyRate' | 'metRate';

interface UserScore {
  namedProScore: number; // 舐めプ度（軸1）
  nominationCount: number; // 指名数（軸2）
}

interface RankingEntry {
  anonymousId: string;
  score: number;
  rank: number;
  isMe: boolean;
}
```
