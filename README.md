# 恋愛自動操縦

> 「もう悩まなくていい。あなたの恋愛、AIが操縦します。」

---

## コンセプト

マッチングアプリで出会い、メッセージを重ねても、結局は同じような会話の繰り返し。そこに疲弊して、会えなくなるのは勿体ない。

そんな現状を解決するために、**恋愛自動操縦**はマッチングアプリの会話をAIが全自動で設計・支援する。

しかし、立ち止まって考えてほしい。

恋愛とは本来、個と個が試行錯誤しながら歩み寄るプロセスである。その過程で生まれる緊張・失敗・喜びこそが人間的な成長につながる。その大切なプロセスをAIに全部やらせることは、相手の気持ちを踏み躙る倫理的に問題のある行為ではないだろうか。

**それこそが、人を本当の意味でダメにするサービスである。**

さらに、自分のメッセージが他ユーザーにコピーされることで、他人の思考力まで奪っていく連鎖構造を持つ。便利であればあるほど、ユーザーは自分で考えなくなる。


---

## 主要機能

| 機能 | 概要 |
|---|---|
| キャラクター設定 | 話し方・趣味・成功パターンを登録して自分専用AIを作る |
| 相手ペルソナ分析 | 趣味タグを選ぶだけでAIが相手の人物像を推定する |
| メッセージ生成 | ゴール（電話/ご飯）を設定するだけで最初のメッセージを複数パターン生成 |
| 会話ルート設計 | ゴールまでの会話シナリオを一覧で提示。返信が来たら練り直しも可能 |
| 振り返りレポート | 会話終了後にAIが返信率・改善点・次回戦略を自動生成 |
| 舐めプスコア | AIを使うたびに「舐めプ度」が上昇。使うほど自分で考えなくなっていく |
| 指名ランキング | 自分のメッセージが他ユーザーにコピーされるたびに「指名数」が加算される |

---

## 「人をダメにする」エコシステム

```
P-04（競争心ユーザー）
  ランキング1位(自分がどれだけ会えたかを誇示すること)を目指して必死にメッセージを磨く
       |
       | 磨いたメッセージをコピー
       v
P-01（初心者）・P-02（経験者）
  無思考でコピーして使う → 自分で考えなくなる
       |
       v
P-03（データ重視）
  舐めプスコアの推移を分析してさらにAI依存を深める
```

頑張る人が頑張らない人を養う。このエコシステム全体が「人をダメにする」仕組みを形成している。

---

## 技術スタック

| 項目 | 技術 |
|---|---|
| フロントエンド | Next.js（TypeScript） |
| AIエンジン | Amazon Bedrock（Claude 3 Sonnet） |
| バックエンド | AWS Lambda + API Gateway |
| データベース | Amazon DynamoDB |
| 認証 | Amazon Cognito |
| ホスティング | AWS Amplify |
| IaC | AWS CDK（TypeScript） |

---

## システム構成（9 Unit）

```
love_automatic_machine/
  ├── frontend/          # Unit 8: Next.js フロントエンド
  ├── backend/
  │   ├── message/       # Unit 1: AIメッセージ生成エンジン
  │   ├── conversation/  # Unit 2: 会話ルート設計エンジン
  │   ├── persona/       # Unit 3: 相手ペルソナ分析エンジン
  │   ├── auth/          # Unit 4: ユーザー管理・認証
  │   ├── character/     # Unit 5: キャラクター設定管理
  │   ├── results/       # Unit 6: 実績トラッキング・レポート
  │   └── score/         # Unit 7: 舐めプスコア・指名ランキング
  └── infrastructure/    # Unit 9: AWS CDK
```

---

## 設計ドキュメント（Inception Phase）

| ドキュメント | 内容 |
|---|---|
| [要件定義書](aidlc-docs/inception/requirements/requirements.md) | 機能要件・非機能要件・技術スタック |
| [ペルソナ定義](aidlc-docs/inception/user-stories/personas.md) | 4ペルソナの詳細・エコシステム構造 |
| [ユーザーストーリー](aidlc-docs/inception/user-stories/stories.md) | 6エピック・13ストーリー（Given/When/Then形式） |
| [アプリケーション設計書](aidlc-docs/inception/application-design/application-design.md) | アーキテクチャ・コンポーネント・データフロー |
| [Unit定義書](aidlc-docs/inception/application-design/unit-of-work.md) | 9 Unit・モノレポ構成 |
| [Unit依存関係](aidlc-docs/inception/application-design/unit-of-work-dependency.md) | Unit間依存マトリクス・開発順序 |
| [ストーリーマッピング](aidlc-docs/inception/application-design/unit-of-work-story-map.md) | ストーリーとUnitの対応表 |
| [実行計画書](aidlc-docs/inception/plans/execution-plan.md) | ワークフロー図・フェーズ詳細 |
