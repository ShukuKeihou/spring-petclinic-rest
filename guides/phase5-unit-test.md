# Phase 5: 単体テスト

**担当エージェント**: QA エンジニア

**目標**:
- Controller の単体テストを実装する
- MockMvc を使用したテストを作成する
- テストカバレッジを確認する
- テストコードをコミットしてPRを出す

**前提条件**: Phase 4 (Issue #4) がマージ済みであること

---

## 5.1 Phase 5 Issue の作成

Phase 4（実装）が完了したので、Phase 5（単体テスト）の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/05_unit_test.yml`

**Issue タイトル**: `[単体テスト] ペットホテル機能の単体テスト実装`

**前工程 Issue**: Phase 4 の Issue 番号を記入（例: `#4`）

---

## 5.2 QA エンジニアエージェントの役割

QA エンジニアエージェントは以下をサポートします：
- 包括的なテストケース設計
- エッジケースの洗い出し
- モックの適切な使用
- テストコード実装

---

## 5.3 ブランチ作成

```bash
# 最新の master を取得
git checkout master
git pull origin master

# Phase 5 用のブランチ作成
git checkout -b feature/phase5-unit-test
```

---

## 5.4 QA エージェントへのプロンプト例

```
PetHotelStayRestController の単体テストを実装してください：

【参考】
VisitRestControllerTests.java のパターンに従ってください。

【テストケース】
1. getPetHotelStaySuccess - 正常取得
2. getPetHotelStayNotFound - 存在しないID
3. listPetHotelStaysSuccess - 一覧取得
4. createPetHotelStaySuccess - 正常作成
5. updatePetHotelStaySuccess - 正常更新
6. updatePetHotelStayNotFound - 存在しないID更新
7. deletePetHotelStaySuccess - 正常削除
8. deletePetHotelStayNotFound - 存在しないID削除

【要件】
- MockMvc を使用
- @WithMockUser でセキュリティテスト
- ClinicService をモック
- JSON形式の検証

完全なJavaコードを生成してください。
```

---

## 5.5 テストコード実装

QA エージェントの出力を基に、[src/test/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestControllerTests.java](../src/test/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestControllerTests.java) を作成します。

**実装する8つのテストケース**:

1. `testGetPetHotelStaySuccess()` - 正常取得（200 OK）
2. `testGetPetHotelStayNotFound()` - 存在しないID（404 NOT_FOUND）
3. `testListPetHotelStaysSuccess()` - 一覧取得（200 OK）
4. `testCreatePetHotelStaySuccess()` - 新規作成（201 CREATED）
5. `testUpdatePetHotelStaySuccess()` - 正常更新（200 OK）
6. `testUpdatePetHotelStayNotFound()` - 存在しないID更新（404 NOT_FOUND）
7. `testDeletePetHotelStaySuccess()` - 正常削除（204 NO_CONTENT）
8. `testDeletePetHotelStayNotFound()` - 存在しないID削除（404 NOT_FOUND）

---

## 5.6 テスト実行

```bash
# 新しいテストのみ実行
mvn test -Dtest=PetHotelStayRestControllerTests

# すべてのテスト実行
mvn test
```

**期待結果**:
```
Tests run: 8, Failures: 0, Errors: 0, Skipped: 0
```

---

## 5.7 テストカバレッジレポート生成

```bash
# カバレッジレポート生成
mvn jacoco:report
```

**レポートの場所**: `target/site/jacoco/index.html`

**確認ポイント**:
1. **全体のカバレッジ率**:
   - Line Coverage: 85% 以上が目標
   - Branch Coverage: 66% 以上が目標

2. **パッケージごとの確認**:
   - `org.springframework.samples.petclinic.rest.controller`: PetHotelStayRestController のカバレッジを確認
   - `org.springframework.samples.petclinic.service`: ClinicService のカバレッジを確認

3. **カバレッジが低い箇所の特定**:
   - 赤色で表示される行: テストでカバーされていない
   - 黄色で表示される分岐: 部分的にしかカバーされていない

---

## 5.8 コミット

```bash
git add src/test/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestControllerTests.java
git commit -m "Add PetHotelStay controller tests"
```

---

## 5.9 Phase 5 の Pull Request 作成と承認

### リモートへプッシュ

```bash
git push origin feature/phase5-unit-test
```

### Pull Request 作成

**PRタイトル**: `Phase 5: ペットホテル機能の単体テスト (#5)`

**PR説明**:
```markdown
## Phase 5: 単体テスト

Closes #5
Depends on: #4

## 概要
PetHotelStayRestController の単体テストを実装しました。

## 変更内容
- ✅ PetHotelStayRestControllerTests を作成
- ✅ 8つのテストケースを実装
  - 正常系: 取得、一覧、作成、更新、削除
  - 異常系: 存在しないIDの取得、更新、削除
- ✅ MockMvc を使用したコントローラーテスト
- ✅ @WithMockUser によるセキュリティテスト
- ✅ ClinicService のモック化

## エージェント活用
- QA Engineer エージェント (`@qa-engineer`) を活用してテスト設計・実装

## テスト結果
```bash
mvn test -Dtest=PetHotelStayRestControllerTests
# Tests run: 8, Failures: 0, Errors: 0, Skipped: 0
# Time elapsed: 2.5 s
```

## カバレッジ
- Line Coverage: 90% (目標: 85%以上)
- Branch Coverage: 75% (目標: 66%以上)

## チェックリスト
- [x] すべてのテストがパスする
- [x] カバレッジ目標を達成している
- [x] 正常系・異常系の両方をカバーしている
- [x] セキュリティ設定がテストされている
- [x] 次工程（Phase 6: 統合テスト）への引き継ぎ情報が明確

## レビュー観点
1. テストケースの網羅性
2. モックの適切な使用
3. アサーションの妥当性
4. テストコードの可読性

## 次のステップ
このPRがマージされたら、Phase 6（統合テスト）のIssue #6を作成してください。
```

**ラベル**: `phase:unit-test`, `status:review`

### マージとクリーンアップ

```bash
git checkout master
git pull origin master
git branch -d feature/phase5-unit-test
```

---

## 完了チェックリスト

Phase 5 が完了したら、以下を確認してください：

- [ ] Issue #5 が作成されている
- [ ] PetHotelStayRestControllerTests が実装されている
- [ ] 8つのテストケースがすべてパスしている
- [ ] カバレッジレポートが生成されている
- [ ] PR #5 が作成され、マージされている
- [ ] Issue #5 がクローズされている
- [ ] feature/phase5-unit-test ブランチが削除されている

---

## トラブルシューティング

### テストが失敗する

**解決策**:
```bash
# MapStruct の生成クラスを再生成
mvn clean compile

# 特定のテストだけ実行して原因を特定
mvn test -Dtest=PetHotelStayRestControllerTests#testGetPetHotelStaySuccess
```

### カバレッジが目標に達しない

**解決策**:
- カバレッジレポートで赤色の行を確認
- QA エージェントに追加のテストケースを依頼
- エッジケースや例外ケースのテストを追加

### モックが正しく動作しない

**解決策**:
- `@MockBean` アノテーションが正しいか確認
- `given(...).willReturn(...)` の設定を確認
- デバッグログを有効にして動作を確認

---

## 次のステップ

**Phase 5 が完了したら、[Phase 6: 統合テスト](phase6-integration-test.md)に進んでください。**

---

[メインガイドに戻る](../HANDS_ON_GUIDE.md)
