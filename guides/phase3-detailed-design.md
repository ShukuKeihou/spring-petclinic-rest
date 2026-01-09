# Phase 3: 詳細設計

**担当エージェント**: テックリード

**目標**:
- データベーススキーマを設計する
- REST API仕様を定義する
- DTO設計を行う
- バリデーション仕様を定義する

**前提条件**: Phase 2 (Issue #2) がマージ済みであること

---

## 3.1 Phase 3 Issue の作成

Phase 2（基本設計）が完了したので、Phase 3（詳細設計）の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/03_detailed_design.yml`

**Issue タイトル**: `[詳細設計] ペットホテル機能のAPI・データベース設計`

**前工程 Issue**: Phase 2 の Issue 番号を記入（例: `#2`）

---

## 3.2 テックリードエージェントの役割

テックリードエージェントは以下をサポートします：
- データベーススキーマ設計
- REST API 設計
- セキュリティ設計
- 実装詳細の決定

---

## 3.3 テックリードエージェントへのプロンプト例（データベース設計）

```
以下のエンティティに対応するデータベーススキーマを設計してください：

【エンティティ】
PetHotelStay
- id: Integer (主キー)
- checkInDate: LocalDate
- checkOutDate: LocalDate
- roomNumber: String
- status: String
- pet: Pet (ManyToOne)

【既存テーブル】
- pets (id, name, birth_date, type_id, owner_id)
- visits (id, pet_id, visit_date, description)

【要件】
- H2 データベース用のスキーマ
- 適切な外部キー制約
- インデックスの設定
- テストデータの準備

CREATE TABLE文とサンプルINSERT文を生成してください。
```

---

## 3.4 データベーススキーマ設計

テックリードエージェントの出力例：

```sql
CREATE TABLE IF NOT EXISTS pet_hotel_stays (
  id INTEGER IDENTITY PRIMARY KEY,
  pet_id INTEGER NOT NULL,
  check_in_date DATE,
  check_out_date DATE,
  room_number VARCHAR(10),
  status VARCHAR(20)
);

ALTER TABLE pet_hotel_stays
  ADD CONSTRAINT fk_pet_hotel_stay_pet
  FOREIGN KEY (pet_id) REFERENCES pets (id);

CREATE INDEX idx_pet_hotel_stay_pet_id
  ON pet_hotel_stays (pet_id);

-- テストデータ
INSERT INTO pet_hotel_stays VALUES (1, 7, '2023-01-05', '2023-01-10', 'R101', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (2, 8, '2023-02-15', '2023-02-20', 'R102', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (3, 8, '2023-12-20', '2023-12-27', 'R103', 'RESERVED');
```

---

## 3.5 REST API 設計

テックリードエージェントへのプロンプト例（API設計）：

```
ペットホテル予約管理のREST APIを設計してください：

【既存パターン】
- Visit API: GET/POST/PUT/DELETE /api/visits
- Pet API: GET/POST/PUT/DELETE /api/pets

【要件】
- RESTful な設計
- 適切なHTTPメソッド
- 適切なステータスコード
- セキュリティ設定（@PreAuthorize）

以下を含めてください：
1. エンドポイント一覧
2. リクエスト/レスポンス形式
3. HTTPステータスコード
4. 必要なロール
```

テックリードエージェントの出力例：

| メソッド | エンドポイント | 説明 | ステータスコード | ロール |
|---------|---------------|------|----------------|--------|
| GET | /api/pethotelstays | すべての予約一覧 | 200 OK | OWNER_ADMIN |
| GET | /api/pethotelstays/{id} | 特定の予約取得 | 200 OK / 404 NOT_FOUND | OWNER_ADMIN |
| POST | /api/pethotelstays | 新規予約作成 | 201 CREATED | OWNER_ADMIN |
| PUT | /api/pethotelstays/{id} | 予約更新 | 200 OK / 404 NOT_FOUND | OWNER_ADMIN |
| DELETE | /api/pethotelstays/{id} | 予約削除 | 204 NO_CONTENT / 404 NOT_FOUND | OWNER_ADMIN |

**リクエスト例 (POST)**:
```json
{
  "checkInDate": "2024-03-01",
  "checkOutDate": "2024-03-05",
  "roomNumber": "R201",
  "status": "RESERVED",
  "petId": 1
}
```

**レスポンス例 (GET)**:
```json
{
  "id": 1,
  "checkInDate": "2024-03-01",
  "checkOutDate": "2024-03-05",
  "roomNumber": "R201",
  "status": "RESERVED",
  "petId": 1
}
```

---

## 3.6 詳細設計レビュー

```
以下の設計をレビューしてください：

1. データベーススキーマに不足はありませんか？
2. API設計は RESTful ですか？
3. セキュリティ上の懸念はありませんか？
4. パフォーマンス上の問題はありませんか？
5. 他に考慮すべき点はありますか？
```

**演習**:
1. テックリードエージェントとDB設計を詰める
2. API設計を確認
3. 設計書を作成（ドキュメントまたはコメント）

---

## 3.7 Phase 3 の Pull Request 作成と承認

### 詳細設計ドキュメント作成

```bash
# Phase 3 用のブランチ作成
git checkout -b feature/phase3-detailed-design

# 詳細設計書を作成
# docs/phase3-detailed-design.md に以下を含める：
# - データベーススキーマ（CREATE TABLE文）
# - REST API仕様（エンドポイント、リクエスト/レスポンス）
# - DTO設計
# - バリデーション仕様

git add docs/phase3-detailed-design.md
git commit -m "Phase 3: Add detailed design document

Related Issue: #3

- Define database schema (DDL)
- Define REST API specifications
- Define DTO structures
- Define validation rules"

git push origin feature/phase3-detailed-design
```

### Pull Request 作成

**PRタイトル**: `Phase 3: ペットホテル機能の詳細設計 (#3)`

**PR説明**:
```markdown
## Phase 3: 詳細設計

Closes #3
Depends on: #2

## 概要
ペットホテル機能のREST API仕様とデータベーススキーマを詳細に設計しました。

## 変更内容
- ✅ 詳細設計書を作成 (`docs/phase3-detailed-design.md`)
- ✅ データベーススキーマ定義（CREATE TABLE, INDEX, FK）
- ✅ REST APIエンドポイント設計（5つのCRUD操作）
- ✅ DTO設計（PetHotelStayDto, PetHotelStayFieldsDto）
- ✅ バリデーション仕様定義
- ✅ HTTPステータスコード定義

## エージェント活用
- Tech Lead エージェント (`@techlead`) を活用して設計

## API設計の要点
- RESTful な設計原則に従う
- 適切なHTTPメソッドとステータスコードを使用
- セキュリティ設定（@PreAuthorize）を考慮

## チェックリスト
- [x] データベーススキーマが完全
- [x] REST API仕様が明確
- [x] DTO設計が完了
- [x] バリデーション仕様が定義されている
- [x] 次工程（Phase 4: 実装）への引き継ぎ情報が明確

## レビュー観点
1. API設計のRESTfulness
2. データベース設計の正規化
3. セキュリティ考慮
4. パフォーマンス考慮

## 次のステップ
このPRがマージされたら、Phase 4（実装）のIssue #4を作成してください。
```

**ラベル**: `phase:detailed-design`, `status:review`

### マージとクリーンアップ

```bash
git checkout master
git pull origin master
git branch -d feature/phase3-detailed-design
```

---

## 完了チェックリスト

Phase 3 が完了したら、以下を確認してください：

- [ ] Issue #3 が作成されている
- [ ] 詳細設計書 (`docs/phase3-detailed-design.md`) が作成されている
- [ ] PR #3 が作成され、マージされている
- [ ] Issue #3 がクローズされている
- [ ] feature/phase3-detailed-design ブランチが削除されている

---

## トラブルシューティング

### SQL文の書き方がわからない

**解決策**:
- [src/main/resources/db/h2/schema.sql](../src/main/resources/db/h2/schema.sql) を参考にしてください
- テックリードエージェントに既存のスキーマファイルを参照させてください

### API設計が RESTful かどうか不安

**解決策**:
- 既存の Visit API や Pet API のパターンを確認してください
- テックリードエージェントに RESTful 設計の観点からレビューを依頼してください

---

## 次のステップ

**Phase 3 が完了したら、[Phase 4: 実装](phase4-implementation.md)に進んでください。**

---

[メインガイドに戻る](../HANDS_ON_GUIDE.md)
