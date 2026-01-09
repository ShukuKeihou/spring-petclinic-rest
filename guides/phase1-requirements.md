# Phase 1: 要求開発

**担当エージェント**: プロダクトマネージャー（PM）

**目標**:
- ビジネス要件を整理する
- ユーザーストーリーを作成する
- Issue Form を使用してIssueを作成する
- 要件定義書を作成してPRを出す

---

## 💡 Issue Driven Development のポイント

**各工程（Phase）ごとにIssueとPRを作成します**：

1. **Issue作成**: 各工程の開始時に、Issue Formを使用してIssueを作成
2. **作業実施**: Issueで定義した内容を実施（設計書作成、コード実装など）
3. **PR作成**: 作業完了後、Pull Requestを作成してレビュー依頼
4. **承認・マージ**: レビュー承認後にマージし、Issueをクローズ
5. **次工程へ**: 前工程のIssue番号を参照して、次のIssueを作成

**メリット**:
- 🎯 各工程の目的と成果物が明確
- 📝 作業履歴が自動的に記録される
- 👥 工程ごとのレビューで品質向上
- 🔄 フィードバックループが早い

---

## 1.1 PM エージェントの役割

プロダクトマネージャーエージェントは以下をサポートします：
- ビジネス要件の整理
- ユーザーストーリーの作成
- Issue の構造化
- 受け入れ基準の定義

---

## 1.2 PM エージェントへのプロンプト例

PM エージェント (pm) を選択して、以下のプロンプトを入力してください：

![PM Agent](../images/pm-agent.png)

```
以下のビジネス要件に基づいて、GitHub Issueを作成してください：

【背景】
ペットクリニックを訪れる飼い主から、旅行や出張中にペットを預かってほしいという要望が多く寄せられている。

【目的】
ペットホテル機能を実装し、予約管理ができるようにする。

【既存システム】
- Visit エンティティでペットの来院記録を管理している
- Pet エンティティでペット情報を管理している
- REST APIでCRUD操作を提供している

【必要な機能】
- チェックイン日・チェックアウト日の記録
- 部屋番号の管理
- ステータス管理（予約済み、チェックイン済み、チェックアウト済み）
- REST APIでの予約管理

既存のVisitパターンを参考に、シンプルで実装可能なIssueを作成してください。
```

---

## 1.3 GitHub Issue Form の使用

PM エージェントと相談しながら、Phase 1（要求開発）のIssue Formを使用してIssueを作成します。

GitHub リポジトリの **Issues** タブ → **New Issue** に移動し、以下のIssue Formを選択:

**Issue Form**: `Phase 1: 要求開発 (Requirements Development)`

このIssue Formには以下のフィールドが含まれています:
- 機能名
- 概要
- 背景
- ユーザーストーリー
- 機能要件
- 非機能要件
- 受け入れ基準
- 技術的考慮事項

自動的に適切なラベル（`phase:requirement`, `status:planning`）が付与されます。

---

## 1.4 Issue 作成

PM エージェントの出力を基に、GitHub の Issue Form を使用して Phase 1 の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/01_requirement.yml`

**Issue タイトル**: `[要求] ペットホテル機能の追加`

**Issue 本文** (PM エージェントが生成した内容を Issue Form に入力します。以下は例です):

```markdown
## 概要
ペットを預かるペットホテル機能を実装する。

## 背景
クリニックを訪れる飼い主から、旅行や出張中にペットを預かってほしいという要望が多く寄せられている。

## ユーザーストーリー
- 飼い主として、ペットを預ける予約ができる
- スタッフとして、予約状況を確認できる
- スタッフとして、チェックイン・チェックアウトを記録できる

## 機能要件
- ペットホテルの予約情報を管理できる
- チェックイン日とチェックアウト日を記録できる
- 部屋番号を管理できる
- ステータス管理（RESERVED, CHECKED_IN, CHECKED_OUT）
- REST APIで予約のCRUD操作ができる

## 非機能要件
- 既存のコード構造とパターンに従う
- 適切なテストを含める
- セキュリティ設定を適用する

## 受け入れ基準
- [ ] すべてのテストがパスする
- [ ] API経由でペットホテル予約のCRUD操作ができる
- [ ] 既存機能に影響を与えない
- [ ] コードレビューで承認される

## 技術的制約
- Visit エンティティのパターンを参考にする
- 既存の Pet との関連付けを活用する
- Spring Data JPA を使用する
```

**演習**:
1. PM エージェントと対話しながらIssueを洗練させる
2. Phase 1 の Issue Form を使用して GitHub に Issue を作成
3. Issue が作成されると、自動的に `phase:requirement` と `status:planning` ラベルが付与されます

**重要**: 
- 各工程（Phase）ごとに個別の Issue を作成します
- Phase 1 が完了したら、Phase 2（基本設計）の Issue を作成します
- 各 Issue には前工程の Issue 番号を記録します

**複数 Issue の関連付け**:
- 複数の要件や実装をまとめて扱う場合、関連する複数の Issue 番号を指定できます
- Issue Form では「#123, #124」または「#123 #124」のように記載します
- Pull Request でも同様に複数の Issue を関連付けることができます
- 例：複数の要件を統合した設計、複数の実装をまとめたテストなど

---

## 1.5 Phase 1 の Pull Request 作成と承認

### Issue #1 の作業完了確認

**作業内容チェックリスト**:
- [x] ビジネス要件が明確に定義されている
- [x] ユーザーストーリーが記載されている
- [x] 機能要件・非機能要件が整理されている
- [x] 受け入れ基準が定義されている
- [x] 技術的制約が明確

### Phase 1 用の設計ドキュメント作成

Issue #1で定義した要件を、設計ドキュメントとして整理します：

```bash
# ドキュメントディレクトリ作成
mkdir -p docs

# 要件定義書を作成（エディタで作成）
# docs/phase1-requirements.md
```

**docs/phase1-requirements.md の内容例**:
```markdown
# Phase 1: 要求開発 - ペットホテル機能

Related Issue: #1

## 概要
ペットを預かるペットホテル機能を実装する。

## ビジネス背景
クリニックを訪れる飼い主から、旅行や出張中にペットを預かってほしいという要望が多く寄せられている。

## ユーザーストーリー
- 飼い主として、ペットを預ける予約ができる
- スタッフとして、予約状況を確認できる
- スタッフとして、チェックイン・チェックアウトを記録できる

## 機能要件
1. ペットホテルの予約情報を管理できる
2. チェックイン日とチェックアウト日を記録できる
3. 部屋番号を管理できる
4. ステータス管理（RESERVED, CHECKED_IN, CHECKED_OUT）
5. REST APIで予約のCRUD操作ができる

## 非機能要件
- 既存のコード構造とパターンに従う
- 適切なテストを含める
- セキュリティ設定を適用する

## 技術的制約
- Visit エンティティのパターンを参考にする
- 既存の Pet との関連付けを活用する
- Spring Data JPA を使用する

## 受け入れ基準
- [ ] すべてのテストがパスする
- [ ] API経由でペットホテル予約のCRUD操作ができる
- [ ] 既存機能に影響を与えない
- [ ] コードレビューで承認される
```

### ブランチ作成とコミット

```bash
# Phase 1 用のブランチ作成
git checkout -b feature/phase1-requirements

# ドキュメントをコミット
git add docs/phase1-requirements.md
git commit -m "Phase 1: Add requirements document for pet hotel feature

Related Issue: #1

- Define business requirements
- Define user stories
- Define functional and non-functional requirements
- Define acceptance criteria"

# リモートにプッシュ
git push origin feature/phase1-requirements
```

### Pull Request 作成

GitHub で Pull Request を作成:

**PRタイトル**: `Phase 1: ペットホテル機能の要求開発 (#1)`

**PR説明**:
```markdown
## Phase 1: 要求開発

Closes #1

## 概要
ペットホテル機能のビジネス要件とユーザーストーリーを定義しました。

## 変更内容
- ✅ 要件定義書を作成 (`docs/phase1-requirements.md`)
- ✅ ビジネス背景を明確化
- ✅ ユーザーストーリーを定義
- ✅ 機能要件・非機能要件を整理
- ✅ 技術的制約を明確化
- ✅ 受け入れ基準を定義

## エージェント活用
- PM エージェント (`@pm`) を活用して要件を整理

## チェックリスト
- [x] ビジネス要件が明確
- [x] ユーザーストーリーが記載されている
- [x] 機能要件が具体的
- [x] 受け入れ基準が測定可能
- [x] 次工程（Phase 2: 基本設計）への引き継ぎ情報が明確

## レビュー観点
1. ビジネス要件が十分に明確か
2. ユーザーストーリーが適切か
3. 技術的制約が妥当か
4. 受け入れ基準が測定可能か

## 次のステップ
このPRがマージされたら、Phase 2（基本設計）のIssue #2を作成してください。
```

**ラベル**: `phase:requirement`, `status:review`

### レビューと承認

**セルフレビュー観点**:
1. ✅ 要件が明確で実装可能か
2. ✅ 既存システムとの整合性はあるか
3. ✅ スコープは適切か（大きすぎない）
4. ✅ 次工程への引き継ぎ情報は十分か

**講師レビュー**:
- 要件定義の妥当性を確認
- 承認コメントを記入
- Pull Request を承認

### マージとクリーンアップ

```bash
# GitHub でマージ後
git checkout master
git pull origin master
git branch -d feature/phase1-requirements
```

**Issue #1 のクローズ**:
PRがマージされると、「Closes #1」により Issue #1 が自動的にクローズされます。

---

## 完了チェックリスト

Phase 1 が完了したら、以下を確認してください：

- [ ] Issue #1 が作成されている
- [ ] 要件定義書 (`docs/phase1-requirements.md`) が作成されている
- [ ] PR #1 が作成され、マージされている
- [ ] Issue #1 がクローズされている
- [ ] feature/phase1-requirements ブランチが削除されている

---

## トラブルシューティング

### PM エージェントが期待通りの出力をしない

**解決策**:
- プロンプトを具体的にし、参考ファイルや期待する出力形式を明示してください
- 既存のVisitエンティティの内容を含めて質問してください

### Issue Form が見つからない

**解決策**:
- `.github/ISSUE_TEMPLATE/` ディレクトリに Issue Form が存在するか確認してください
- リポジトリをフォークした際に Issue Template が含まれているか確認してください

---

## 次のステップ

**Phase 1 が完了したら、[Phase 2: 基本設計](phase2-basic-design.md)に進んでください。**

---

[メインガイドに戻る](../HANDS_ON_GUIDE.md)
