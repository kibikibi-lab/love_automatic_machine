# アプリケーション設計プラン

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04

---

## 設計成果物チェックリスト

- [x] components.md — コンポーネント定義・責務
- [x] component-methods.md — メソッドシグネチャ
- [x] services.md — サービス定義・オーケストレーション
- [x] component-dependency.md — 依存関係・通信パターン
- [x] application-design.md — 上記の統合ドキュメント

---

## 設計方針に関する質問

以下の質問に回答してください。
`[Answer]:` タグに回答を記入し、完了したら「完了」とお知らせください。
わからない場合は「お任せ」でOKです。

---

### Question 1 APIの設計スタイル

フロントエンド（Next.js）とバックエンド（Lambda）間のAPI設計をどうしますか？

**参考：**
- **A) REST API** — シンプルで一般的。GET/POST/PUT/DELETEで操作を表現。学習コストが低い
- **B) GraphQL** — 必要なデータだけ取得できる。フロントエンドの柔軟性が高いが設計が複雑
- **C) REST API（推奨）** — ハッカソン規模ではRESTが最もシンプルで実装しやすい

A) REST API
B) GraphQL
C) お任せ（推奨に従う）
D) Other (please describe after [Answer]: tag below)

[Answer]: C

---

### Question 2 Bedrockの呼び出しモデル

Amazon Bedrockで使用するAIモデルをどれにしますか？

**参考：**
- **A) Claude 3 Sonnet** — 品質と速度のバランスが良い。日本語対応も優秀。**ハッカソン向けに最もおすすめ**
- **B) Claude 3 Haiku** — 最速・最安。品質はSonnetより低いが、レスポンス速度重視なら有効
- **C) Amazon Titan** — AWSネイティブ。日本語品質はClaudeより劣る場合がある

A) Claude 3 Sonnet（推奨）
B) Claude 3 Haiku
C) Amazon Titan
D) お任せ（推奨に従う）
E) Other (please describe after [Answer]: tag below)

[Answer]: A

---

### Question 3 コンポーネント間の通信方式

Unit間（Lambda関数間）の通信をどうしますか？

**参考：**
- **A) 直接API呼び出し（同期）** — シンプル。レスポンスをすぐ返せる。ハッカソン向き
- **B) Amazon SQS経由（非同期）** — 疎結合で堅牢。ただし設計が複雑になる
- **C) お任せ** — ハッカソン規模では**A（直接API呼び出し）**が適切

A) 直接API呼び出し（同期）
B) Amazon SQS経由（非同期）
C) お任せ（推奨に従う）
D) Other (please describe after [Answer]: tag below)

[Answer]: C

---

### Question 4 認証の実装範囲

ユーザー認証をどこまで実装しますか？

**参考：**
- **A) フル実装** — Cognitoでサインアップ・ログイン・パスワードリセットまで実装
- **B) 最小限** — Cognitoでログインのみ。サインアップはシンプルなフォーム
- **C) モック** — ハッカソンのデモ用に認証をスキップして固定ユーザーで動作させる

A) フル実装
B) 最小限（推奨）
C) モック（デモ優先）
D) Other (please describe after [Answer]: tag below)

[Answer]: C

---

回答が完了したら「完了」とお知らせください。
