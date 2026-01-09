# Phase 6: 統合テスト

**担当エージェント**: QA マネージャー

**目標**:
- アプリケーション全体の動作を確認する
- API統合テストを実施する
- 既存機能への影響を確認する
- テスト報告書を作成する

**前提条件**: Phase 5 (Issue #5) がマージ済みであること

---

## 6.1 Phase 6 Issue の作成

Phase 5（単体テスト）が完了したので、Phase 6（統合テスト）の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/06_integration_test.yml`

**Issue タイトル**: `[統合テスト] ペットホテル機能の統合テスト実施`

**前工程 Issue**: Phase 5 の Issue 番号を記入（例: `#5`）

---

## 6.2 QA マネージャーエージェントの役割

QA マネージャーエージェントは以下をサポートします：
- 統合テストシナリオの設計
- エンドツーエンドテスト
- 品質基準の確認
- テスト報告書作成

---

## 6.3 QA マネージャーエージェントへのプロンプト例

```
アプリケーションの統合テストを実施します。
以下の確認項目でテストシナリオを提案してください：

1. すべての単体テストがパスするか
2. アプリケーションが正常に起動するか
3. データベースマイグレーションが正常に動作するか
4. 全APIエンドポイントが正常に動作するか
5. 既存機能に影響がないか
```

---

## 6.4 ビルドとテスト実行

```bash
# 完全ビルド
mvn clean install

# アプリケーション起動
mvn spring-boot:run
```

**期待結果**: アプリケーションが `http://localhost:9966` で正常に起動

---

## 6.5 API 統合テスト

QA マネージャーエージェントへのプロンプト：

```
以下のAPIエンドポイントをcurlでテストするスクリプトを作成してください：

【認証】
- ユーザー: admin
- パスワード: admin

【エンドポイント】
1. GET /api/pethotelstays - 一覧取得（期待: 200 OK）
2. GET /api/pethotelstays/1 - 特定取得（期待: 200 OK）
3. POST /api/pethotelstays - 新規作成（期待: 201 CREATED）
4. PUT /api/pethotelstays/{id} - 更新（期待: 200 OK）
5. DELETE /api/pethotelstays/{id} - 削除（期待: 204 NO_CONTENT）

各テストケースの期待結果とアサーションを含めてください。
```

---

## 6.6 手動テスト実行

### テストケース1: すべての予約取得

```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays
```

**期待結果**: 200 OK、3件の予約データが返される

---

### テストケース2: 特定の予約取得

```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays/1
```

**期待結果**: 200 OK、id=1 の予約データが返される

---

### テストケース3: 新規予約作成

```bash
curl -u admin:admin -X POST http://localhost:9966/petclinic/api/pethotelstays \
  -H "Content-Type: application/json" \
  -d '{
    "checkInDate": "2024-03-01",
    "checkOutDate": "2024-03-05",
    "roomNumber": "R201",
    "status": "RESERVED",
    "petId": 1
  }'
```

**期待結果**: 201 CREATED、Location ヘッダー、作成された予約データが返される

---

### テストケース4: 予約更新

```bash
curl -u admin:admin -X PUT http://localhost:9966/petclinic/api/pethotelstays/1 \
  -H "Content-Type: application/json" \
  -d '{
    "checkInDate": "2023-01-05",
    "checkOutDate": "2023-01-10",
    "roomNumber": "R102",
    "status": "CHECKED_OUT",
    "petId": 7
  }'
```

**期待結果**: 200 OK、更新された予約データが返される

---

### テストケース5: 予約削除

```bash
curl -u admin:admin -X DELETE http://localhost:9966/petclinic/api/pethotelstays/3
```

**期待結果**: 204 NO_CONTENT

---

### テストケース6: 既存機能への影響確認

```bash
# Pet API が正常に動作するか確認
curl -u admin:admin http://localhost:9966/petclinic/api/pets

# Visit API が正常に動作するか確認
curl -u admin:admin http://localhost:9966/petclinic/api/visits
```

**期待結果**: 両方とも 200 OK、既存データが正常に取得できる

---

## 6.7 統合テスト時のカバレッジ確認

```bash
# 完全なテストとカバレッジレポート生成
mvn clean test jacoco:report
```

**レポートの確認**:
- `target/site/jacoco/index.html` をブラウザで開く
- 統合テスト実行により、カバレッジが向上しているか確認

**確認項目**:
1. 全体のカバレッジ率が目標値（Line: 85%, Branch: 66%）を達成しているか
2. 新規実装した PetHotelStay 関連クラスのカバレッジは十分か
3. 統合テストにより追加でカバーされた箇所はどこか

---

## 6.8 テスト報告書作成

QA マネージャーエージェントへのプロンプト：

```
以下のテスト結果を基に、テスト報告書を作成してください：

【実施したテスト】
- 単体テスト: 8件すべてパス
- テストカバレッジ: Line 90%, Branch 75%達成
- ビルドテスト: 成功
- 統合テスト: 6つのAPIエンドポイントすべて正常動作
- 既存機能確認: Pet API, Visit API ともに正常動作

【不具合】
なし

【品質評価】
すべてのテストがパスし、カバレッジ目標を達成。既存機能に影響なし。リリース可能。
```

テスト報告書を `docs/phase6-test-report.md` として作成します。

---

## 6.9 Phase 6 の Pull Request 作成と承認

### ドキュメント作成とコミット

```bash
# Phase 6 用のブランチ作成
git checkout -b feature/phase6-integration-test

# テスト報告書を作成
# docs/phase6-test-report.md

git add docs/phase6-test-report.md
git commit -m "Add integration test report"

git push origin feature/phase6-integration-test
```

### Pull Request 作成

**PRタイトル**: `Phase 6: ペットホテル機能の統合テスト (#6)`

**PR説明**:
```markdown
## Phase 6: 統合テスト

Closes #6
Depends on: #5

## 概要
ペットホテル機能の統合テストを実施し、全体の品質を確認しました。

## 実施内容
### ビルドテスト
- ✅ mvn clean install: 成功
- ✅ アプリケーション起動: 正常

### API統合テスト（curl）
- ✅ GET /api/pethotelstays - 一覧取得（200 OK）
- ✅ GET /api/pethotelstays/1 - 特定取得（200 OK）
- ✅ POST /api/pethotelstays - 新規作成（201 CREATED）
- ✅ PUT /api/pethotelstays/1 - 更新（200 OK）
- ✅ DELETE /api/pethotelstays/3 - 削除（204 NO_CONTENT）

### 既存機能への影響確認
- ✅ Pet API: 正常動作
- ✅ Visit API: 正常動作

## エージェント活用
- QA Manager エージェント (`@qa-manager`) を活用してテスト設計・実施

## テスト結果サマリー
- **単体テスト**: 全8件パス
- **統合テスト**: 全6ケースパス
- **カバレッジ**: Line 90%, Branch 75%
- **不具合**: なし

## 品質評価
**判定: 合格** - すべてのテストがパスし、既存機能に影響なし。リリース可能な品質です。

## チェックリスト
- [x] すべてのテストケースがパスしている
- [x] カバレッジ目標を達成している
- [x] 既存機能に影響がない
- [x] テスト報告書を作成している
- [x] 次工程（Phase 7: 最終レビュー）への引き継ぎ情報が明確

## レビュー観点
1. テスト網羅性
2. 品質評価の妥当性
3. 既存機能への影響確認
4. ドキュメントの完全性

## 次のステップ
このPRがマージされたら、Phase 7（最終レビュー）のIssue #7を作成してください。
```

**ラベル**: `phase:integration-test`, `status:review`

### マージとクリーンアップ

```bash
git checkout master
git pull origin master
git branch -d feature/phase6-integration-test
```

---

## 完了チェックリスト

Phase 6 が完了したら、以下を確認してください：

- [ ] Issue #6 が作成されている
- [ ] 統合テストがすべて完了している
- [ ] テスト報告書が作成されている
- [ ] PR #6 が作成され、マージされている
- [ ] Issue #6 がクローズされている
- [ ] feature/phase6-integration-test ブランチが削除されている

---

## トラブルシューティング

### アプリケーションが起動しない

**解決策**:
```bash
# ログを確認
mvn spring-boot:run

# ポート競合を確認
lsof -i :9966

# データベースの初期化を確認
cat src/main/resources/db/h2/schema.sql
```

### API が 401 エラーを返す

**解決策**:
- 認証情報（admin:admin）が正しいか確認
- Spring Security の設定を確認
- アプリケーションログでエラーを確認

---

## 次のステップ

**Phase 6 が完了したら、[Phase 7: 最終レビュー](phase7-final-review.md)に進んでください。**

---

[メインガイドに戻る](../HANDS_ON_GUIDE.md)
