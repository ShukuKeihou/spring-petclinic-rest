# Phase 4: 実装

**担当エージェント**: シニアデベロッパー

**目標**:
- データベーススキーマを実装する
- Entity, Repository, Service, Controller を実装する
- DTO と Mapper を実装する
- 実装コードをコミットしてPRを出す

**前提条件**: Phase 3 (Issue #3) がマージ済みであること

---

## 4.1 Phase 4 Issue の作成

Phase 3（詳細設計）が完了したので、Phase 4（実装）の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/04_implementation.yml`

**Issue タイトル**: `[実装] ペットホテル機能の実装`

**前工程 Issue**: Phase 3 の Issue 番号を記入（例: `#3`）

---

## 4.2 シニアデベロッパーエージェントの役割

シニアデベロッパーエージェントは以下をサポートします：
- 高品質なコード実装
- 既存パターンの踏襲
- ベストプラクティスの適用
- コードレビュー観点の提供

---

## 4.3 ブランチ作成

```bash
# 最新の master を取得
git checkout master
git pull origin master

# Phase 4 用のブランチ作成
git checkout -b feature/phase4-implementation
```

---

## 4.4 データベーススキーマ追加

### ステップ1: スキーマファイル編集

シニアデベロッパーエージェントへのプロンプト：

```
以下の設計に基づいて、H2データベーススキーマを実装してください：

【ファイル】
src/main/resources/db/h2/schema.sql

【追加するテーブル】
pet_hotel_stays テーブル（id, pet_id, check_in_date, check_out_date, room_number, status）

【制約】
- 外部キー: pet_id -> pets(id)
- インデックス: pet_id

既存のスキーマファイルに追記する形でSQLを生成してください。
```

[src/main/resources/db/h2/schema.sql](../src/main/resources/db/h2/schema.sql) に追加:

```sql
CREATE TABLE IF NOT EXISTS pet_hotel_stays (
  id INTEGER IDENTITY PRIMARY KEY,
  pet_id INTEGER NOT NULL,
  check_in_date DATE,
  check_out_date DATE,
  room_number VARCHAR(10),
  status VARCHAR(20)
);
ALTER TABLE pet_hotel_stays ADD CONSTRAINT fk_pet_hotel_stay_pet FOREIGN KEY (pet_id) REFERENCES pets (id);
CREATE INDEX idx_pet_hotel_stay_pet_id ON pet_hotel_stays (pet_id);
```

### ステップ2: テストデータ追加

[src/main/resources/db/h2/data.sql](../src/main/resources/db/h2/data.sql) に追加:

```sql
INSERT INTO pet_hotel_stays VALUES (1, 7, '2023-01-05', '2023-01-10', 'R101', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (2, 8, '2023-02-15', '2023-02-20', 'R102', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (3, 8, '2023-12-20', '2023-12-27', 'R103', 'RESERVED');
```

### ステップ3: コミット

```bash
git add src/main/resources/db/h2/
git commit -m "Add pet_hotel_stays table schema and test data"
```

---

## 4.5 Entity 作成

シニアデベロッパーエージェントへのプロンプト：

```
以下の設計に基づいて PetHotelStay エンティティを実装してください：

【パッケージ】
org.springframework.samples.petclinic.model

【設計】
- BaseEntity を継承
- フィールド: checkInDate (LocalDate), checkOutDate (LocalDate), roomNumber (String), status (String), pet (Pet)
- JPA アノテーション: @Entity, @Table, @Column, @ManyToOne, @JoinColumn, @NotNull
- getter/setter メソッド

【参考】
Visit.java と同様のパターンで実装してください。

完全なJavaコードを生成してください。
```

ファイルを作成してコミット:

```bash
git add src/main/java/org/springframework/samples/petclinic/model/PetHotelStay.java
git commit -m "Add PetHotelStay entity"
```

---

## 4.6 Repository 作成

シニアデベロッパーエージェントへのプロンプト：

```
VisitRepository のパターンに従って、PetHotelStay 用の Repository を実装してください：

【必要なファイル】
1. PetHotelStayRepository.java (インターフェース)
2. SpringDataPetHotelStayRepository.java (Spring Data JPA実装)

【メソッド】
- findById(int id)
- findAll()
- save(PetHotelStay)
- delete(PetHotelStay)
- findByPetId(Integer petId) - カスタムクエリ

完全なJavaコードを2ファイル分生成してください。
```

ファイルを作成してコミット:

```bash
git add src/main/java/org/springframework/samples/petclinic/repository/
git commit -m "Add PetHotelStay repository"
```

---

## 4.7 Service 実装

シニアデベロッパーエージェントへのプロンプト：

```
ClinicService に PetHotelStay 関連のメソッドを追加してください：

【ファイル】
1. ClinicService.java (インターフェース) - メソッド定義を追加
2. ClinicServiceImpl.java (実装) - フィールド、コンストラクタ、メソッド実装を追加

【メソッド】
- findPetHotelStayById(int id)
- findAllPetHotelStays()
- savePetHotelStay(PetHotelStay)
- deletePetHotelStay(PetHotelStay)
- findPetHotelStaysByPetId(Integer petId)

【注意】
- @Transactional アノテーションを適切に使用
- 読み取り専用は @Transactional(readOnly = true)

差分形式で追加すべきコードを提示してください。
```

変更をコミット:

```bash
git add src/main/java/org/springframework/samples/petclinic/service/
git commit -m "Add PetHotelStay service methods"
```

---

## 4.8 DTO と Mapper 作成

シニアデベロッパーエージェントへのプロンプト：

```
VisitDto/VisitMapper のパターンに従って、PetHotelStay 用の DTO と Mapper を実装してください：

【必要なファイル】
1. PetHotelStayDto.java - レスポンス用DTO（idを含む）
2. PetHotelStayFieldsDto.java - リクエスト用DTO（idを含まない）
3. PetHotelStayMapper.java - MapStruct マッパー

【フィールド】
- checkInDate, checkOutDate, roomNumber, status, petId

【注意】
- @JsonFormat でLocalDateをフォーマット
- @NotNull でバリデーション
- MapStruct で pet.id <-> petId のマッピング

完全なJavaコードを3ファイル分生成してください。
```

ファイルを作成してコミット:

```bash
git add src/main/java/org/springframework/samples/petclinic/rest/dto/ src/main/java/org/springframework/samples/petclinic/mapper/
git commit -m "Add PetHotelStay DTOs and Mapper"
```

---

## 4.9 REST Controller 実装

シニアデベロッパーエージェントへのプロンプト：

```
VisitRestController のパターンに従って、PetHotelStayRestController を実装してください：

【エンドポイント】
- GET /api/pethotelstays - 全件取得
- GET /api/pethotelstays/{id} - 1件取得
- POST /api/pethotelstays - 新規作成
- PUT /api/pethotelstays/{id} - 更新
- DELETE /api/pethotelstays/{id} - 削除

【セキュリティ】
- @PreAuthorize("hasRole(@roles.OWNER_ADMIN)")

【ステータスコード】
- GET: 200 OK / 404 NOT_FOUND
- POST: 201 CREATED (Location ヘッダー付き)
- PUT: 200 OK / 404 NOT_FOUND
- DELETE: 204 NO_CONTENT / 404 NOT_FOUND

完全なJavaコードを生成してください。
```

ファイルを作成してコミット:

```bash
git add src/main/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestController.java
git commit -m "Add PetHotelStay REST controller"
```

---

## 4.10 ビルドとテスト実行

```bash
# コンパイル（MapStruct生成）
mvn clean compile

# テスト実行
mvn test
```

---

## 4.11 Phase 4 の Pull Request 作成と承認

### リモートへプッシュ

```bash
git push origin feature/phase4-implementation
```

### Pull Request 作成

**PRタイトル**: `Phase 4: ペットホテル機能の実装 (#4)`

**PR説明**:
```markdown
## Phase 4: 実装

Closes #4
Depends on: #3

## 概要
ペットホテル機能の全レイヤー（Entity, Repository, Service, Controller）を実装しました。

## 変更内容
### データベース
- ✅ pet_hotel_stays テーブルのスキーマを追加
- ✅ 外部キー制約とインデックスを設定
- ✅ テストデータを3件追加

### バックエンド
- ✅ PetHotelStay エンティティを実装
- ✅ PetHotelStayRepository を実装（Spring Data JPA）
- ✅ ClinicService に PetHotelStay メソッドを追加
- ✅ PetHotelStayDto と PetHotelStayFieldsDto を実装
- ✅ PetHotelStayMapper を実装（MapStruct）
- ✅ PetHotelStayRestController を実装

## エージェント活用
- Senior Developer エージェント (`@senior-dev`) を活用して実装

## 実装の要点
1. **Visitパターンの踏襲**: 既存コードとの一貫性を保持
2. **Spring Data JPA**: カスタムクエリでペット別検索を実装
3. **MapStruct**: DTOとエンティティの自動マッピング
4. **RESTful API**: 標準的なCRUD操作を提供

## ビルド結果
```bash
mvn clean compile
# BUILD SUCCESS
```

## チェックリスト
- [x] すべてのレイヤーが実装されている
- [x] MapStruct が正常に動作する
- [x] コンパイルが成功する
- [x] 既存パターンに従っている
- [x] 次工程（Phase 5: 単体テスト）への引き継ぎ情報が明確

## レビュー観点
1. コード品質とスタイルの統一性
2. エラーハンドリングの適切性
3. セキュリティ設定
4. 既存パターンとの整合性

## 次のステップ
このPRがマージされたら、Phase 5（単体テスト）のIssue #5を作成してください。
```

**ラベル**: `phase:implementation`, `status:review`

### マージとクリーンアップ

```bash
git checkout master
git pull origin master
git branch -d feature/phase4-implementation
```

---

## 完了チェックリスト

Phase 4 が完了したら、以下を確認してください：

- [ ] Issue #4 が作成されている
- [ ] すべてのコード（Entity, Repository, Service, Controller, DTO, Mapper）が実装されている
- [ ] PR #4 が作成され、マージされている
- [ ] Issue #4 がクローズされている
- [ ] feature/phase4-implementation ブランチが削除されている

---

## トラブルシューティング

### MapStruct が動作しない

**解決策**:
```bash
# クリーンビルドを実行
mvn clean compile

# target/generated-sources/annotations/ にマッパーが生成されているか確認
ls target/generated-sources/annotations/org/springframework/samples/petclinic/mapper/
```

### コンパイルエラーが発生する

**解決策**:
- インポート文が正しいか確認してください
- 既存のコードとパッケージ構造が一致しているか確認してください
- シニアデベロッパーエージェントにエラーメッセージを示してデバッグを依頼してください

---

## 次のステップ

**Phase 4 が完了したら、[Phase 5: 単体テスト](phase5-unit-test.md)に進んでください。**

---

[メインガイドに戻る](../HANDS_ON_GUIDE.md)
