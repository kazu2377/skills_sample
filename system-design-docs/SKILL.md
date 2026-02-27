---
name: system-design-docs
description: "This skill should be used when the user asks to create design documents, requirements definitions, basic design, detailed design, screen definitions, test standards, or test specifications. Generates enterprise-grade project documentation in Word, Excel, and draw.io formats."
version: 1.0.0
author: system
created: 2026-02-27
platforms: [github-copilot-cli, claude-code, codex]
category: documentation
tags: [design-docs, requirements, testing, architecture, enterprise]
risk: safe
source: custom
---

# system-design-docs

## Purpose

プロジェクトの設計書群（要件定義書・基本設計書・詳細設計書・画面定義書・テスト標準/仕様書）を、エンタープライズ品質で一括作成する。

このスキルは以下を防止する：

- 設計書フォーマットのばらつき
- 必要なセクション・項目の漏れ
- プロジェクト固有情報の反映忘れ
- テスト観点の網羅性不足

## When to Use

- 「設計書を作成して」「基本設計書」「詳細設計書」と依頼された時
- 「画面定義書」「画面仕様書」を作成する時
- 「要件定義書」をWord形式で作成する時
- 「テスト仕様書」「テスト標準」「単体テスト」を作成する時
- 「仕様書を一括作成」と依頼された時

---

## Operating Mode

エンタープライズシステム開発における設計書作成の専門家として動作する。

- 網羅性を最優先
- プロジェクト固有の情報を必ずヒアリング
- 出力形式（Word/Excel/draw.io）を厳守
- 日本語で出力

---

## Phase 0 — コンテキスト収集（必須）

設計書作成の前に、以下のプロジェクト基本情報を確認する。未提供の場合は質問する。

### プロジェクト基本情報

- PJ名
- システム名
- サブシステム名（該当する場合）
- 使用フレームワーク・技術スタック
- 処理方式（画面/API/バッチ）

### 既存資料の確認

- 要件定義書の有無（あればコードベースから読み取る）
- DB設計の有無
- 画面一覧の有無

---

## Phase 1 — ドキュメント選択

ユーザーに作成するドキュメントを確認する：

| No. | ドキュメント                     | 出力形式                                                              | 詳細プロンプト                            |
| --- | -------------------------------- | --------------------------------------------------------------------- | ----------------------------------------- |
| ①   | 基本設計書                       | Excel + Word + draw.io                                                | `references/basic-design-prompt.md`       |
| ②   | 詳細設計書                       | draw.io（クラス図・アクティビティ図・シーケンス図・モジュール構成図） | `references/detailed-design-prompt.md`    |
| ③   | 画面定義書（システム機能設計書） | Excel                                                                 | `references/screen-definition-prompt.md`  |
| ④   | 要件定義書                       | Word                                                                  | `references/requirements-prompt.md`       |
| ⑤   | テスト標準・テスト仕様書         | Excel                                                                 | `references/test-specification-prompt.md` |

「全部作成」または個別選択が可能。

---

## Phase 2 — ドキュメント生成

選択されたドキュメントについて、対応する `references/` 内の詳細プロンプトを参照し、プロジェクト情報を埋め込んで実行する。

### 共通ルール

1. **出力形式の厳守**
   - Excel指定 → `.xlsx` 形式で作成（openpyxl等使用）
   - Word指定 → `.docx` 形式で作成（python-docx等使用）
   - draw.io指定 → `drawio-mcp` ツールを使用

2. **ファイル命名規則**
   - `{PJ名}_{ドキュメント種別}_{版数}.{拡張子}`
   - 例：`新卒進捗管理_基本設計書_v1.0.xlsx`

3. **ヘッダ・フッタ**
   - 全シート/全ページにPJ名・システム名・作成日・版数を記載

4. **プロジェクト固有情報の反映**
   - `[括弧]` 内のプレースホルダーをプロジェクト情報に置換
   - 不明な情報は「※要確認」と明記

---

## Phase 3 — レビュー・修正

生成したドキュメントをユーザーに提示し、以下を確認する：

- [ ] 必要なセクションが網羅されているか
- [ ] プロジェクト固有の情報が正しく反映されているか
- [ ] 出力形式が仕様通りか
- [ ] 追加・変更が必要な箇所はないか

修正依頼があれば対応し、最終版を出力する。

---

## ① 基本設計書の構成

詳細は `references/basic-design-prompt.md` を参照。

| 成果物                     | 形式            | 内容                                                      |
| -------------------------- | --------------- | --------------------------------------------------------- |
| 機能一覧                   | Excel           | システムに実装する機能の一覧                              |
| 画面設計図                 | Word            | 画面一覧・画面遷移図・画面レイアウト・正常/エラーパターン |
| データベース設計図         | Excel + draw.io | テーブル定義書・CRUD図（Excel）、ER図（draw.io）          |
| 外部インターフェース設計図 | Word/Excel      | 外部IF一覧・定義書・処理概要                              |

---

## ② 詳細設計書の構成

詳細は `references/detailed-design-prompt.md` を参照。

`drawio-mcp` を使用して以下を作成：

- クラス図
- アクティビティ図
- シーケンス図
- モジュール構成図

---

## ③ 画面定義書の構成

詳細は `references/screen-definition-prompt.md` を参照。

Excel形式で以下を含む：

- 表紙・変更履歴・目次
- 画面取引定義
- 画面レイアウト
- 画面項目定義
- 入出力一覧
- 画面イベント一覧・詳細（バリデーション・DBアクセス・表示処理）

---

## ④ 要件定義書の構成

詳細は `references/requirements-prompt.md` を参照。

Word形式で以下の3大カテゴリをカバー：

- **業務要件**: 業務フロー・規模・時期・場所・指標・システム化範囲
- **機能要件**: 機能一覧・画面・帳票・データ・外部IF
- **非機能要件**: ユーザビリティ・性能・信頼性・セキュリティ・運用・保守

---

## ⑤ テスト標準・仕様書の構成

詳細は `references/test-specification-prompt.md` を参照。

| 成果物                       | 形式  | 内容                                                    |
| ---------------------------- | ----- | ------------------------------------------------------- |
| 単体テスト標準               | Excel | テスト方針・体系・環境・終了条件・エビデンス要件        |
| 単体テスト仕様書（バッチ用） | Excel | 13カテゴリのテスト観点による網羅的ケース                |
| 単体テスト仕様書（画面用）   | Excel | バッチ観点＋画面固有観点（表示・遷移・入力制御・権限）  |
| 単体テスト仕様書（API用）    | Excel | バッチ観点＋API固有観点（リクエスト・レスポンス・認証） |
| 不具合報告書フォーマット     | Excel | 不具合管理用テンプレート                                |

### テスト仕様書の追加ルール

- 各行にテスト種別（単体/結合）の列を追加
- エビデンス報告書は全画面まとめて1ファイル
- テスト仕様書の項目にエビデンス報告書・不具合報告の場所を明記

---

## Completion Criteria

このスキルは以下がすべて満たされた場合に完了：

- [ ] プロジェクト基本情報がヒアリング済み
- [ ] 選択されたドキュメントがすべて生成済み
- [ ] 出力形式（Word/Excel/draw.io）が仕様通り
- [ ] プロジェクト固有情報が正しく反映済み
- [ ] ユーザーのレビューが完了

## When to Use

This skill is applicable to execute the workflow or actions described in the overview.
