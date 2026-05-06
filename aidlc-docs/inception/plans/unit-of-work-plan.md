# Unit分解プラン

**プロジェクト**: 恋愛自動操縦  
**作成日**: 2026-05-04

---

## 生成ステップ

- [x] Step 1: unit-of-work.md — Unit定義・責務・コード構成
- [x] Step 2: unit-of-work-dependency.md — Unit間依存関係マトリクス
- [x] Step 3: unit-of-work-story-map.md — ストーリーとUnitのマッピング

---

## 確認質問

Application Designで9 Unit構成は確定済みです。
コード構成に関する点だけ確認します。

`[Answer]:` タグに回答を記入し、完了したら「完了」とお知らせください。

---

### Question 1 リポジトリ構成

GitHubリポジトリをどう構成しますか？

**参考：**
- **A) モノレポ（1リポジトリに全Unit）** — 管理が一元化される。ハッカソンでは全体を1つのリポジトリで提出しやすい。**推奨**
- **B) マルチレポ（UnitごとにリポジトリをわけるA）** — 独立性が高いが、ハッカソン期間では管理が煩雑になる

A) モノレポ（推奨）
B) マルチレポ
C) Other (please describe after [Answer]: tag below)

[Answer]: A

---

### Question 2 ディレクトリ構成

モノレポの場合、どのディレクトリ構成にしますか？

**参考：**

```
A案（機能別）:
renai-jidosoji/
  ├── frontend/          # Unit 8: Next.js
  ├── backend/
  │   ├── message/       # Unit 1
  │   ├── conversation/  # Unit 2
  │   ├── persona/       # Unit 3
  │   ├── auth/          # Unit 4
  │   ├── character/     # Unit 5
  │   ├── results/       # Unit 6
  │   └── score/         # Unit 7
  └── infrastructure/    # Unit 9: CDK

B案（シンプル）:
renai-jidosoji/
  ├── frontend/          # Unit 8
  ├── functions/         # Unit 1〜7をまとめる
  └── cdk/               # Unit 9
```

A) A案（機能別・Unit対応が明確）← 推奨
B) B案（シンプル）
C) Other (please describe after [Answer]: tag below)

[Answer]: A

---

回答が完了したら「完了」とお知らせください。
