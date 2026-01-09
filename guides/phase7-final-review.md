# Phase 7: 最終レビューと振り返り

**担当エージェント**: リード

**目標**:
- Pull Request を作成する
- コードレビューを実施する
- マージを実行する
- 振り返りを行う

**前提条件**: Phase 6 (Issue #6) がマージ済みであること

---

## 7.1 Phase 7 Issue の作成

Phase 6（統合テスト）が完了したので、Phase 7（最終レビュー）の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/07_final_review.yml`

**Issue タイトル**: `[最終レビュー] ペットホテル機能の最終レビューと受け入れ`

**前工程 Issue**: Phase 6 の Issue 番号を記入（例: `#6`）

---

## 7.2 リードエージェントの役割

リードエージェントは以下をサポートします：
- Pull Request の品質確認
- コードレビュー観点の提示
- マージ判断
- ドキュメント確認

---

## 7.3 最終 Pull Request 作成

### ブランチ作成（必要な場合）

すべての実装が完了している場合は、Phase 1-6 のブランチをマージした最終的な master から作業します。

---

## 7.4 リードエージェントへのプロンプト例（PR説明文作成）

```
以下の変更内容に基づいて、最終的な Pull Request の説明文を作成してください：

【変更内容】
- データベーススキーマ追加: pet_hotel_stays テーブル
- PetHotelStay エンティティ追加
- PetHotelStayRepository 追加
- ClinicService 拡張
- PetHotelStayRestController 追加
- DTO と Mapper 追加
- テストコード追加（8件）

【テスト結果】
- すべての単体テストがパス
- 統合テストが正常動作
- カバレッジ: Line 90%, Branch 75%
- 既存機能に影響なし

【各フェーズ】
- Phase 1: 要求開発（PM）
- Phase 2: 基本設計（Architect）
- Phase 3: 詳細設計（Tech Lead）
- Phase 4: 実装（Senior Developer）
- Phase 5: 単体テスト（QA Engineer）
- Phase 6: 統合テスト（QA Manager）
- Phase 7: 最終レビュー（Lead）

適切なPR説明文とチェックリストを作成してください。
```

---

## 7.5 コードレビュー

リードエージェントへのプロンプト：

```
以下のファイルをレビューしてください：

【レビュー観点】
1. コード品質
2. 既存パターンとの整合性
3. セキュリティ
4. パフォーマンス
5. 保守性

【ファイル】
- PetHotelStay.java
- PetHotelStayRestController.java
- PetHotelStayRestControllerTests.java

改善提案があれば指摘してください。
```

リードエージェントのレビュー例：

```markdown
## コードレビュー結果

### 全体評価
✅ 実装パターンが既存コードに統一されており、良好です。

### 詳細コメント

#### PetHotelStay.java
✅ BaseEntity の継承が適切
✅ JPA アノテーションが正しい
✅ バリデーションが適切

💡 改善提案:
`status` フィールドをStringではなくEnumにすることを検討してください。
これにより、タイプセーフティが向上します。

```java
public enum StayStatus {
    RESERVED, CHECKED_IN, CHECKED_OUT
}
```

#### PetHotelStayRestController.java
✅ セキュリティ設定が適切
✅ HTTPステータスコードが正しい
✅ エラーハンドリングが適切

💡 改善提案:
`checkInDate` が `checkOutDate` より後の日付でないかのバリデーションを追加することをお勧めします。

#### PetHotelStayRestControllerTests.java
✅ テストカバレッジが十分
✅ モックが適切に使用されている
✅ エッジケースがテストされている

### 総評
**承認**: マージ可能です。上記の改善提案は将来的な課題として記録してください。
```

---

## 7.6 マージ

レビューが承認されたら、以下の手順でマージします:

1. GitHub PR画面で "Merge pull request" をクリック
2. **Squash and merge** を選択（推奨）
3. "Confirm merge" をクリック

```bash
# ローカルで master を更新
git checkout master
git pull origin master
```

---

## 7.7 振り返り

リードエージェントと振り返り：

```
今回のハンズオンを振り返ってください：

【良かった点】
【改善点】
【学んだこと】
【次のアクション】
```

**演習**:
1. 各工程でのエージェント活用の効果を振り返る
2. Issue Driven Development の理解度を確認
3. 次回への改善点を記録

振り返りの結果を `docs/phase7-retrospective.md` として記録します。

---

## 7.8 完了確認

### 最終チェックリスト

すべてのフェーズが完了したら、以下を確認してください：

#### 機能確認
- [ ] すべてのAPIエンドポイントが正常に動作する
- [ ] データベースにデータが正しく保存される
- [ ] 既存機能（Pet API, Visit API）に影響がない

#### コード品質
- [ ] すべての単体テストがパスする
- [ ] 統合テストがパスする
- [ ] カバレッジ目標（Line: 85%+, Branch: 66%+）を達成している
- [ ] コードレビューで承認されている

#### ドキュメント
- [ ] Phase 1-6 の設計ドキュメントが作成されている
- [ ] テスト報告書が作成されている
- [ ] 振り返りドキュメントが作成されている

#### Issue Driven Development
- [ ] Issue #1-7 がすべて作成されている
- [ ] PR #1-6 がすべてマージされている
- [ ] すべての Issue がクローズされている

---

## まとめ

このハンズオンを通じて、以下のスキルを習得しました:

✅ **Issue Driven Development** の実践
✅ **GitHub Copilot カスタムエージェント** の活用
✅ 工程ごとの適切なエージェント選択
✅ Git のブランチ戦略
✅ Pull Request の作成と管理
✅ コードレビューのプロセス
✅ Spring Boot での REST API 開発
✅ テスト駆動開発の基礎

---

## エージェント活用のまとめ

| 工程 | エージェント | 活用ポイント |
|------|------------|------------|
| 要求開発 | PM | ビジネス要件を技術要件に変換 |
| 基本設計 | アーキテクト | 既存パターンとの整合性確保 |
| 詳細設計 | テックリード | 実装可能な詳細設計 |
| 実装 | シニアデベロッパー | 高品質なコード生成 |
| 単体テスト | QA エンジニア | 包括的なテストケース |
| 統合・総合テスト | QA マネージャー | エンドツーエンド品質保証 |
| 受け入れ | リード | 全体品質確認とマージ判断 |

---

## 追加課題（時間がある場合）

### 課題1: ステータス管理の改善

```
`status` フィールドをStringからEnumに変更してください。

StayStatus Enum を作成し、PetHotelStay エンティティを更新してください。
```

### 課題2: バリデーション強化

```
以下のバリデーションを実装してください：

1. チェックイン日がチェックアウト日より前であることを検証
2. 同じ部屋番号で期間が重複しないことを検証

カスタムバリデーターを作成してください。
```

### 課題3: 統計API追加

```
現在の稼働状況を取得するAPIを設計・実装してください：

GET /api/pethotelstays/statistics
{
  "totalRooms": 10,
  "occupiedRooms": 7,
  "availableRooms": 3
}
```

### 課題4: ペット別の履歴取得

```
特定のペットのホテル利用履歴を取得するAPIを追加してください：

GET /api/pets/{petId}/hotelstays
```

---

## 参考資料

- [Spring PetClinic REST](https://github.com/spring-petclinic/spring-petclinic-rest)
- [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)

---

## トラブルシューティング

### Q1: エージェントが期待通りの出力をしない

**A**: プロンプトを具体的にし、参考ファイルや期待する出力形式を明示してください。

### Q2: テストが失敗する

**A**: 以下を確認してください:
- MapStruct の生成クラスがビルドされているか（`mvn clean compile`）
- データベーススキーマが正しく適用されているか
- モックの設定が適切か

### Q3: API が 401 エラーを返す

**A**: 認証情報を確認してください:
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays
```

### Q4: エージェントの選択に迷う

**A**: 以下を参考にしてください:
- 「何を作るか」を決める → PM
- 「どう設計するか」を決める → アーキテクト/テックリード
- 「どう実装するか」を決める → シニアデベロッパー
- 「どうテストするか」を決める → QA エンジニア/マネージャー
- 「どう判断するか」を決める → リード

---

## 🎉 お疲れさまでした！

ペットホテル機能の実装が完了しました。Issue Driven Development と GitHub Copilot カスタムエージェントを活用した開発プロセスを体験できました。

この経験を活かして、今後のプロジェクトでも効率的な開発を実践してください！

---

[メインガイドに戻る](../HANDS_ON_GUIDE.md)
