# ペットホテル機能追加ハンズオン - エージェント活用版

## 概要

このハンズオンでは、Spring PetClinic REST アプリケーションに「ペットホテル機能」を追加することで、Issue Driven Development のワークフローと **GitHub Copilot カスタムエージェント** の活用方法を学びます。

**所要時間**: 約300分（5時間）

**学習目標**:
- Issue Driven Development の実践
- GitHub Copilot カスタムエージェントの活用
- 開発工程ごとの適切なエージェント選択
- ブランチ戦略とGitワークフロー
- Pull Request の作成とコードレビュー

## 前提条件

- Java 17 以上
- Maven 3.9 以上
- Git
- GitHub アカウント
- IDE (IntelliJ IDEA, Eclipse, VS Code など)
- **GitHub Copilot サブスクリプション**

## エージェントベース開発フロー

このハンズオンでは、開発工程ごとに専門のエージェントを活用します：

| 工程 | エージェントロール | 主な役割 |
|------|-------------------|---------|
| 要求開発 | プロダクトマネージャー (PM) | Issue作成、要件定義 |
| 基本設計 | ソフトウェアアーキテクト | システム構造設計、エンティティ設計 |
| 詳細設計 | テックリード | API設計、データベース設計 |
| 実装 | シニアデベロッパー | コード実装 |
| 単体テスト | QA エンジニア | テストコード作成 |
| 統合・総合テスト | QA エンジニア/QA マネージャー | API動作確認、品質保証 |
| 受け入れ | リード | PR作成、レビュー、マージ |

## タイムテーブル

| 時間 | セクション | エージェント | 内容 |
|------|----------|-------------|------|
| 0:00-0:30 | セクション0 | - | 環境セットアップと既存機能の理解 |
| 0:30-1:00 | セクション1 | PM | 要求開発（Issue作成） |
| 1:00-1:30 | セクション2 | アーキテクト | 基本設計（システム構造・エンティティ設計） |
| 1:30-2:00 | セクション3 | テックリード | 詳細設計（API・DB設計） |
| 2:00-3:30 | セクション4 | シニアデベロッパー | 実装 |
| 3:30-4:00 | セクション5 | QA エンジニア | 単体テスト |
| 4:00-4:30 | セクション6 | QA エンジニア/マネージャー | 統合・総合テスト |
| 4:30-5:00 | セクション7 | リード | 受け入れとマージ |

---

## セクション0: 環境セットアップと既存機能の理解 (30分)

### 0.1 リポジトリのフォークとクローン

1. GitHub で spring-petclinic-rest リポジトリをフォーク
2. ローカルにクローン:

```bash
git clone https://github.com/YOUR_USERNAME/spring-petclinic-rest.git
cd spring-petclinic-rest
```

### 0.2 アプリケーションの起動

```bash
# ビルドとテスト実行
mvn clean install

# アプリケーション起動
mvn spring-boot:run
```

アプリケーションは `http://localhost:9966` で起動します。

### 0.3 既存 API の確認

Swagger UI でAPIを確認: `http://localhost:9966/petclinic`

主要なエンドポイント:
- `GET /api/owners` - 飼い主一覧
- `GET /api/pets` - ペット一覧
- `GET /api/visits` - 来院記録一覧

**演習**: curl または Postman で以下を実行してください:

```bash
# ペット一覧取得
curl http://localhost:9966/petclinic/api/pets

# 特定のペット取得
curl http://localhost:9966/petclinic/api/pets/1
```

### 0.4 既存コードの構造理解

以下のファイルを確認し、パターンを理解してください:

1. **Entity**: `src/main/java/org/springframework/samples/petclinic/model/Visit.java`
   - ペットとの関連（ManyToOne）
   - 日付フィールド（LocalDate）
   - シンプルな構造

2. **Repository**: `src/main/java/org/springframework/samples/petclinic/repository/springdatajpa/SpringDataVisitRepository.java`
   - Spring Data JPA の利用
   - カスタムクエリメソッド

3. **Service**: `src/main/java/org/springframework/samples/petclinic/service/ClinicServiceImpl.java`
   - トランザクション管理
   - 複数リポジトリの協調

4. **Controller**: `src/main/java/org/springframework/samples/petclinic/rest/controller/VisitRestController.java`
   - REST API の実装
   - セキュリティアノテーション
   - DTOマッパーの利用

**確認ポイント**:
- Visit エンティティがどのようにPetと関連しているか
- CRUD操作がどのように実装されているか
- DTO変換がどのように行われているか

---

## セクション1: 要求開発 - PM エージェント活用 (30分)

### 1.1 PM エージェントの役割

プロダクトマネージャーエージェントは以下をサポートします：
- ビジネス要件の整理
- ユーザーストーリーの作成
- Issue の構造化
- 受け入れ基準の定義

### 1.2 PM エージェントへのプロンプト例

```
@pm 以下のビジネス要件に基づいて、GitHub Issueを作成してください：

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

### 1.3 GitHub Issue Form の使用

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

### 1.4 Issue 作成

PM エージェントの出力を基に、GitHub の Issue Form を使用して Phase 1 の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/01_requirement.yml`

**Issue タイトル**: `[要求] ペットホテル機能の追加`

**Issue 本文** (PM エージェントが生成した内容を Issue Form に入力):

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

## セクション2: 基本設計 - アーキテクトエージェント活用 (30分)

### 2.0 Phase 2 Issue の作成

Phase 1（要求開発）が完了したら、Phase 2（基本設計）の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/02_basic_design.yml`

**Issue タイトル**: `[基本設計] ペットホテル機能の追加`

**前工程 Issue**: Phase 1 の Issue 番号を記入（複数の要件を統合する場合は、カンマまたはスペース区切りで複数記入可：例 #123, #124）

### 2.1 アーキテクトエージェントの役割

ソフトウェアアーキテクトエージェントは以下をサポートします：
- システムアーキテクチャの設計
- エンティティモデルの設計
- 既存パターンとの整合性確認
- 技術選択の推奨

### 2.2 ブランチ作成

Git-Flow に従い、feature ブランチを作成します:

```bash
# 最新の master を取得
git checkout master
git pull origin master

# feature ブランチ作成
git checkout -b feature/pet-hotel

# 確認
git branch
```

### 2.3 アーキテクトエージェントへのプロンプト例

```
@architect 以下の既存コードベースを分析して、ペットホテル機能の基本設計を行ってください：

【既存エンティティ】
- Visit: ペットの来院記録（pet, date, description）
- Pet: ペット情報（name, birthDate, type, owner）
- Owner: 飼い主情報

【要件】
- チェックイン日・チェックアウト日の記録
- 部屋番号の管理
- ステータス管理（RESERVED, CHECKED_IN, CHECKED_OUT）
- Pet との関連付け

【制約】
- Visit と同様のパターンを踏襲する
- 既存のレイヤー構造（Entity, Repository, Service, Controller）に従う

以下を設計してください：
1. PetHotelStay エンティティの構造
2. Pet エンティティとの関係
3. 必要なフィールドとデータ型
4. JPA アノテーション
```

### 2.4 エンティティ設計

アーキテクトエージェントの出力例：

**PetHotelStay エンティティ設計**:

```java
@Entity
@Table(name = "pet_hotel_stays")
public class PetHotelStay extends BaseEntity {

    @Column(name = "check_in_date")
    @NotNull
    private LocalDate checkInDate;

    @Column(name = "check_out_date")
    @NotNull
    private LocalDate checkOutDate;

    @Column(name = "room_number")
    private String roomNumber;

    @Column(name = "status")
    @NotNull
    private String status; // RESERVED, CHECKED_IN, CHECKED_OUT

    @ManyToOne
    @JoinColumn(name = "pet_id")
    @NotNull
    private Pet pet;

    // Getters and Setters
}
```

### 2.5 アーキテクチャ確認

アーキテクトエージェントと以下を確認：

```
@architect 以下の点について確認してください：

1. Visit パターンとの一貫性は保たれていますか？
2. BaseEntity の継承は適切ですか？
3. Pet との ManyToOne 関係は適切ですか？
4. status フィールドは String で良いですか、それとも Enum にすべきですか？
5. 他に考慮すべきアーキテクチャ上の懸念点はありますか？
```

**演習**:
1. アーキテクトエージェントとエンティティ設計を詰める
2. 設計ドキュメントとして記録（メモやコメント）

---

## セクション3: 詳細設計 - テックリードエージェント活用 (30分)

### 3.1 テックリードエージェントの役割

テックリードエージェントは以下をサポートします：
- データベーススキーマ設計
- REST API 設計
- セキュリティ設計
- 実装詳細の決定

### 3.2 テックリードエージェントへのプロンプト例（データベース設計）

```
@techlead 以下のエンティティに対応するデータベーススキーマを設計してください：

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

### 3.3 データベーススキーマ設計

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

### 3.4 REST API 設計

テックリードエージェントへのプロンプト例（API設計）：

```
@techlead ペットホテル予約管理のREST APIを設計してください：

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

### 3.5 詳細設計レビュー

```
@techlead 以下の設計をレビューしてください：

1. データベーススキーマに不足はありませんか？
2. API設計は RESTful ですか？
3. セキュリティ上の懸念はありますか？
4. パフォーマンス上の問題はありますか？
5. 他に考慮すべき点はありますか？
```

**演習**:
1. テックリードエージェントとDB設計を詰める
2. API設計を確認
3. 設計書を作成（ドキュメントまたはコメント）

---

## セクション4: 実装 - シニアデベロッパーエージェント活用 (90分)

### 4.1 シニアデベロッパーエージェントの役割

シニアデベロッパーエージェントは以下をサポートします：
- 高品質なコード実装
- 既存パターンの踏襲
- ベストプラクティスの適用
- コードレビュー観点の提供

### 4.2 データベーススキーマ追加 (15分)

#### ステップ1: スキーマファイル編集

```
@senior-dev 以下の設計に基づいて、H2データベーススキーマを実装してください：

【ファイル】
src/main/resources/db/h2/schema.sql

【追加するテーブル】
pet_hotel_stays テーブル（id, pet_id, check_in_date, check_out_date, room_number, status）

【制約】
- 外部キー: pet_id -> pets(id)
- インデックス: pet_id

既存のスキーマファイルに追記する形でSQLを生成してください。
```

シニアデベロッパーエージェントの出力を `src/main/resources/db/h2/schema.sql` に追加:

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

#### ステップ2: テストデータ追加

```
@senior-dev src/main/resources/db/h2/data.sql にサンプルデータを追加してください。
3件のペットホテル予約データを生成してください。
```

`src/main/resources/db/h2/data.sql` に追加:

```sql
INSERT INTO pet_hotel_stays VALUES (1, 7, '2023-01-05', '2023-01-10', 'R101', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (2, 8, '2023-02-15', '2023-02-20', 'R102', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (3, 8, '2023-12-20', '2023-12-27', 'R103', 'RESERVED');
```

#### ステップ3: コミット

```bash
git add src/main/resources/db/h2/
git commit -m "Add pet_hotel_stays table schema and test data"
```

### 4.3 Entity 作成 (15分)

```
@senior-dev 以下の設計に基づいて PetHotelStay エンティティを実装してください：

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

シニアデベロッパーエージェントの出力を `src/main/java/org/springframework/samples/petclinic/model/PetHotelStay.java` に保存:

```java
package org.springframework.samples.petclinic.model;

import jakarta.persistence.*;
import jakarta.validation.constraints.NotNull;
import java.time.LocalDate;

@Entity
@Table(name = "pet_hotel_stays")
public class PetHotelStay extends BaseEntity {

    @Column(name = "check_in_date")
    @NotNull
    private LocalDate checkInDate;

    @Column(name = "check_out_date")
    @NotNull
    private LocalDate checkOutDate;

    @Column(name = "room_number")
    private String roomNumber;

    @Column(name = "status")
    @NotNull
    private String status;

    @ManyToOne
    @JoinColumn(name = "pet_id")
    @NotNull
    private Pet pet;

    public LocalDate getCheckInDate() {
        return this.checkInDate;
    }

    public void setCheckInDate(LocalDate checkInDate) {
        this.checkInDate = checkInDate;
    }

    public LocalDate getCheckOutDate() {
        return this.checkOutDate;
    }

    public void setCheckOutDate(LocalDate checkOutDate) {
        this.checkOutDate = checkOutDate;
    }

    public String getRoomNumber() {
        return this.roomNumber;
    }

    public void setRoomNumber(String roomNumber) {
        this.roomNumber = roomNumber;
    }

    public String getStatus() {
        return this.status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public Pet getPet() {
        return this.pet;
    }

    public void setPet(Pet pet) {
        this.pet = pet;
    }
}
```

コミット:
```bash
git add src/main/java/org/springframework/samples/petclinic/model/PetHotelStay.java
git commit -m "Add PetHotelStay entity"
```

### 4.4 Repository 作成 (15分)

```
@senior-dev VisitRepository のパターンに従って、PetHotelStay 用の Repository を実装してください：

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

#### ファイル1: `src/main/java/org/springframework/samples/petclinic/repository/PetHotelStayRepository.java`

```java
package org.springframework.samples.petclinic.repository;

import org.springframework.dao.DataAccessException;
import org.springframework.samples.petclinic.model.PetHotelStay;

import java.util.Collection;

public interface PetHotelStayRepository {

    PetHotelStay findById(int id) throws DataAccessException;

    Collection<PetHotelStay> findAll() throws DataAccessException;

    void save(PetHotelStay petHotelStay) throws DataAccessException;

    void delete(PetHotelStay petHotelStay) throws DataAccessException;

    Collection<PetHotelStay> findByPetId(Integer petId) throws DataAccessException;
}
```

#### ファイル2: `src/main/java/org/springframework/samples/petclinic/repository/springdatajpa/SpringDataPetHotelStayRepository.java`

```java
package org.springframework.samples.petclinic.repository.springdatajpa;

import org.springframework.context.annotation.Profile;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.Repository;
import org.springframework.data.repository.query.Param;
import org.springframework.samples.petclinic.model.PetHotelStay;
import org.springframework.samples.petclinic.repository.PetHotelStayRepository;

import java.util.Collection;

@Profile("spring-data-jpa")
public interface SpringDataPetHotelStayRepository extends PetHotelStayRepository, Repository<PetHotelStay, Integer> {

    @Override
    PetHotelStay findById(int id);

    @Override
    Collection<PetHotelStay> findAll();

    @Override
    void save(PetHotelStay petHotelStay);

    @Override
    void delete(PetHotelStay petHotelStay);

    @Query("SELECT phs FROM PetHotelStay phs WHERE phs.pet.id = :petId")
    Collection<PetHotelStay> findByPetId(@Param("petId") Integer petId);
}
```

コミット:
```bash
git add src/main/java/org/springframework/samples/petclinic/repository/
git commit -m "Add PetHotelStay repository"
```

### 4.5 Service 実装 (15分)

```
@senior-dev ClinicService に PetHotelStay 関連のメソッドを追加してください：

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

シニアデベロッパーエージェントの指示に従い、以下を追加：

**ClinicService.java に追加**:
```java
// PetHotelStay関連メソッド
PetHotelStay findPetHotelStayById(int id) throws DataAccessException;
Collection<PetHotelStay> findAllPetHotelStays() throws DataAccessException;
void savePetHotelStay(PetHotelStay petHotelStay) throws DataAccessException;
void deletePetHotelStay(PetHotelStay petHotelStay) throws DataAccessException;
Collection<PetHotelStay> findPetHotelStaysByPetId(Integer petId) throws DataAccessException;
```

**ClinicServiceImpl.java に追加**:
```java
private final PetHotelStayRepository petHotelStayRepository;

// コンストラクタに追加
public ClinicServiceImpl(
    // ... 既存のパラメータ
    PetHotelStayRepository petHotelStayRepository) {
    // ... 既存の初期化
    this.petHotelStayRepository = petHotelStayRepository;
}

// メソッド実装
@Override
@Transactional(readOnly = true)
public PetHotelStay findPetHotelStayById(int id) throws DataAccessException {
    return petHotelStayRepository.findById(id);
}

@Override
@Transactional(readOnly = true)
public Collection<PetHotelStay> findAllPetHotelStays() throws DataAccessException {
    return petHotelStayRepository.findAll();
}

@Override
@Transactional
public void savePetHotelStay(PetHotelStay petHotelStay) throws DataAccessException {
    petHotelStayRepository.save(petHotelStay);
}

@Override
@Transactional
public void deletePetHotelStay(PetHotelStay petHotelStay) throws DataAccessException {
    petHotelStayRepository.delete(petHotelStay);
}

@Override
@Transactional(readOnly = true)
public Collection<PetHotelStay> findPetHotelStaysByPetId(Integer petId) throws DataAccessException {
    return petHotelStayRepository.findByPetId(petId);
}
```

コミット:
```bash
git add src/main/java/org/springframework/samples/petclinic/service/
git commit -m "Add PetHotelStay service methods"
```

### 4.6 DTO と Mapper 作成 (15分)

```
@senior-dev VisitDto/VisitMapper のパターンに従って、PetHotelStay 用の DTO と Mapper を実装してください：

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

#### ファイル1: `src/main/java/org/springframework/samples/petclinic/rest/dto/PetHotelStayDto.java`

```java
package org.springframework.samples.petclinic.rest.dto;

import com.fasterxml.jackson.annotation.JsonFormat;
import jakarta.validation.constraints.NotNull;
import java.time.LocalDate;

public class PetHotelStayDto {

    private Integer id;

    @NotNull
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate checkInDate;

    @NotNull
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate checkOutDate;

    private String roomNumber;

    @NotNull
    private String status;

    @NotNull
    private Integer petId;

    // Getters and Setters
    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public LocalDate getCheckInDate() {
        return checkInDate;
    }

    public void setCheckInDate(LocalDate checkInDate) {
        this.checkInDate = checkInDate;
    }

    public LocalDate getCheckOutDate() {
        return checkOutDate;
    }

    public void setCheckOutDate(LocalDate checkOutDate) {
        this.checkOutDate = checkOutDate;
    }

    public String getRoomNumber() {
        return roomNumber;
    }

    public void setRoomNumber(String roomNumber) {
        this.roomNumber = roomNumber;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public Integer getPetId() {
        return petId;
    }

    public void setPetId(Integer petId) {
        this.petId = petId;
    }
}
```

#### ファイル2: `src/main/java/org/springframework/samples/petclinic/rest/dto/PetHotelStayFieldsDto.java`

```java
package org.springframework.samples.petclinic.rest.dto;

import com.fasterxml.jackson.annotation.JsonFormat;
import jakarta.validation.constraints.NotNull;
import java.time.LocalDate;

public class PetHotelStayFieldsDto {

    @NotNull
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate checkInDate;

    @NotNull
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate checkOutDate;

    private String roomNumber;

    @NotNull
    private String status;

    @NotNull
    private Integer petId;

    // Getters and Setters
    public LocalDate getCheckInDate() {
        return checkInDate;
    }

    public void setCheckInDate(LocalDate checkInDate) {
        this.checkInDate = checkInDate;
    }

    public LocalDate getCheckOutDate() {
        return checkOutDate;
    }

    public void setCheckOutDate(LocalDate checkOutDate) {
        this.checkOutDate = checkOutDate;
    }

    public String getRoomNumber() {
        return roomNumber;
    }

    public void setRoomNumber(String roomNumber) {
        this.roomNumber = roomNumber;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public Integer getPetId() {
        return petId;
    }

    public void setPetId(Integer petId) {
        this.petId = petId;
    }
}
```

#### ファイル3: `src/main/java/org/springframework/samples/petclinic/mapper/PetHotelStayMapper.java`

```java
package org.springframework.samples.petclinic.mapper;

import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.springframework.samples.petclinic.model.PetHotelStay;
import org.springframework.samples.petclinic.rest.dto.PetHotelStayDto;
import org.springframework.samples.petclinic.rest.dto.PetHotelStayFieldsDto;

import java.util.Collection;

@Mapper(uses = PetMapper.class)
public interface PetHotelStayMapper {

    @Mapping(source = "pet.id", target = "petId")
    PetHotelStayDto toPetHotelStayDto(PetHotelStay petHotelStay);

    Collection<PetHotelStayDto> toPetHotelStayDtos(Collection<PetHotelStay> petHotelStays);

    @Mapping(source = "petId", target = "pet.id")
    PetHotelStay toPetHotelStay(PetHotelStayDto petHotelStayDto);

    @Mapping(source = "petId", target = "pet.id")
    PetHotelStay toPetHotelStay(PetHotelStayFieldsDto petHotelStayFieldsDto);
}
```

コミット:
```bash
git add src/main/java/org/springframework/samples/petclinic/rest/dto/ src/main/java/org/springframework/samples/petclinic/mapper/
git commit -m "Add PetHotelStay DTOs and Mapper"
```

### 4.7 REST Controller 実装 (15分)

```
@senior-dev VisitRestController のパターンに従って、PetHotelStayRestController を実装してください：

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

`src/main/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestController.java` を作成:

```java
package org.springframework.samples.petclinic.rest.controller;

import jakarta.validation.Valid;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.samples.petclinic.mapper.PetHotelStayMapper;
import org.springframework.samples.petclinic.model.PetHotelStay;
import org.springframework.samples.petclinic.rest.dto.PetHotelStayDto;
import org.springframework.samples.petclinic.rest.dto.PetHotelStayFieldsDto;
import org.springframework.samples.petclinic.service.ClinicService;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.util.UriComponentsBuilder;

import java.util.ArrayList;
import java.util.List;

@RestController
@CrossOrigin(exposedHeaders = "errors, content-type")
@RequestMapping("/api/pethotelstays")
public class PetHotelStayRestController {

    private final ClinicService clinicService;
    private final PetHotelStayMapper petHotelStayMapper;

    public PetHotelStayRestController(ClinicService clinicService, PetHotelStayMapper petHotelStayMapper) {
        this.clinicService = clinicService;
        this.petHotelStayMapper = petHotelStayMapper;
    }

    @PreAuthorize("hasRole(@roles.OWNER_ADMIN)")
    @GetMapping
    public ResponseEntity<List<PetHotelStayDto>> listPetHotelStays() {
        List<PetHotelStay> petHotelStays = new ArrayList<>(clinicService.findAllPetHotelStays());
        if (petHotelStays.isEmpty()) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(new ArrayList<>(petHotelStayMapper.toPetHotelStayDtos(petHotelStays)), HttpStatus.OK);
    }

    @PreAuthorize("hasRole(@roles.OWNER_ADMIN)")
    @GetMapping("/{petHotelStayId}")
    public ResponseEntity<PetHotelStayDto> getPetHotelStay(@PathVariable("petHotelStayId") int petHotelStayId) {
        PetHotelStay petHotelStay = clinicService.findPetHotelStayById(petHotelStayId);
        if (petHotelStay == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(petHotelStayMapper.toPetHotelStayDto(petHotelStay), HttpStatus.OK);
    }

    @PreAuthorize("hasRole(@roles.OWNER_ADMIN)")
    @PostMapping
    public ResponseEntity<PetHotelStayDto> addPetHotelStay(
            @RequestBody @Valid PetHotelStayFieldsDto petHotelStayFieldsDto,
            UriComponentsBuilder ucBuilder) {
        PetHotelStay petHotelStay = petHotelStayMapper.toPetHotelStay(petHotelStayFieldsDto);
        clinicService.savePetHotelStay(petHotelStay);
        PetHotelStayDto petHotelStayDto = petHotelStayMapper.toPetHotelStayDto(petHotelStay);
        HttpHeaders headers = new HttpHeaders();
        headers.setLocation(ucBuilder.path("/api/pethotelstays/{id}").buildAndExpand(petHotelStay.getId()).toUri());
        return new ResponseEntity<>(petHotelStayDto, headers, HttpStatus.CREATED);
    }

    @PreAuthorize("hasRole(@roles.OWNER_ADMIN)")
    @PutMapping("/{petHotelStayId}")
    public ResponseEntity<PetHotelStayDto> updatePetHotelStay(
            @PathVariable("petHotelStayId") int petHotelStayId,
            @RequestBody @Valid PetHotelStayFieldsDto petHotelStayFieldsDto) {
        PetHotelStay currentPetHotelStay = clinicService.findPetHotelStayById(petHotelStayId);
        if (currentPetHotelStay == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        currentPetHotelStay.setCheckInDate(petHotelStayFieldsDto.getCheckInDate());
        currentPetHotelStay.setCheckOutDate(petHotelStayFieldsDto.getCheckOutDate());
        currentPetHotelStay.setRoomNumber(petHotelStayFieldsDto.getRoomNumber());
        currentPetHotelStay.setStatus(petHotelStayFieldsDto.getStatus());
        clinicService.savePetHotelStay(currentPetHotelStay);
        return new ResponseEntity<>(petHotelStayMapper.toPetHotelStayDto(currentPetHotelStay), HttpStatus.OK);
    }

    @PreAuthorize("hasRole(@roles.OWNER_ADMIN)")
    @DeleteMapping("/{petHotelStayId}")
    public ResponseEntity<Void> deletePetHotelStay(@PathVariable("petHotelStayId") int petHotelStayId) {
        PetHotelStay petHotelStay = clinicService.findPetHotelStayById(petHotelStayId);
        if (petHotelStay == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        clinicService.deletePetHotelStay(petHotelStay);
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}
```

コミット:
```bash
git add src/main/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestController.java
git commit -m "Add PetHotelStay REST controller"
```

---

## セクション5: 単体テスト - QA エンジニアエージェント活用 (30分)

### 5.1 QA エンジニアエージェントの役割

QA エンジニアエージェントは以下をサポートします：
- 包括的なテストケース設計
- エッジケースの洗い出し
- モックの適切な使用
- テストコード実装

### 5.2 QA エージェントへのプロンプト例

```
@qa-engineer PetHotelStayRestController の単体テストを実装してください：

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

### 5.3 テストコード実装

`src/test/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestControllerTests.java` を作成:

```java
package org.springframework.samples.petclinic.rest.controller;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.samples.petclinic.mapper.PetHotelStayMapper;
import org.springframework.samples.petclinic.model.Pet;
import org.springframework.samples.petclinic.model.PetHotelStay;
import org.springframework.samples.petclinic.rest.advice.ExceptionControllerAdvice;
import org.springframework.samples.petclinic.rest.dto.PetHotelStayFieldsDto;
import org.springframework.samples.petclinic.service.ClinicService;
import org.springframework.security.test.context.support.WithMockUser;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.verify;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@ContextConfiguration
@WebAppConfiguration
class PetHotelStayRestControllerTests {

    @Autowired
    private PetHotelStayRestController petHotelStayRestController;

    @Autowired
    private PetHotelStayMapper petHotelStayMapper;

    @MockBean
    private ClinicService clinicService;

    private MockMvc mockMvc;

    private List<PetHotelStay> petHotelStays;

    @BeforeEach
    void initPetHotelStays() {
        this.mockMvc = MockMvcBuilders.standaloneSetup(petHotelStayRestController)
            .setControllerAdvice(new ExceptionControllerAdvice())
            .build();

        petHotelStays = new ArrayList<>();

        Pet pet = new Pet();
        pet.setId(1);
        pet.setName("Leo");

        PetHotelStay petHotelStay = new PetHotelStay();
        petHotelStay.setId(1);
        petHotelStay.setPet(pet);
        petHotelStay.setCheckInDate(LocalDate.of(2023, 1, 5));
        petHotelStay.setCheckOutDate(LocalDate.of(2023, 1, 10));
        petHotelStay.setRoomNumber("R101");
        petHotelStay.setStatus("CHECKED_OUT");
        petHotelStays.add(petHotelStay);
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testGetPetHotelStaySuccess() throws Exception {
        given(this.clinicService.findPetHotelStayById(1)).willReturn(petHotelStays.get(0));
        this.mockMvc.perform(get("/api/pethotelstays/1")
                .accept(MediaType.APPLICATION_JSON_VALUE))
            .andExpect(status().isOk())
            .andExpect(content().contentType("application/json"))
            .andExpect(jsonPath("$.id").value(1))
            .andExpect(jsonPath("$.roomNumber").value("R101"))
            .andExpect(jsonPath("$.status").value("CHECKED_OUT"));
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testGetPetHotelStayNotFound() throws Exception {
        given(this.clinicService.findPetHotelStayById(999)).willReturn(null);
        this.mockMvc.perform(get("/api/pethotelstays/999")
                .accept(MediaType.APPLICATION_JSON))
            .andExpect(status().isNotFound());
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testListPetHotelStaysSuccess() throws Exception {
        given(this.clinicService.findAllPetHotelStays()).willReturn(petHotelStays);
        this.mockMvc.perform(get("/api/pethotelstays")
                .accept(MediaType.APPLICATION_JSON))
            .andExpect(status().isOk())
            .andExpect(content().contentType("application/json"))
            .andExpect(jsonPath("$.[0].id").value(1))
            .andExpect(jsonPath("$.[0].roomNumber").value("R101"));
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testCreatePetHotelStaySuccess() throws Exception {
        PetHotelStayFieldsDto newPetHotelStayDto = new PetHotelStayFieldsDto();
        newPetHotelStayDto.setCheckInDate(LocalDate.of(2023, 3, 1));
        newPetHotelStayDto.setCheckOutDate(LocalDate.of(2023, 3, 5));
        newPetHotelStayDto.setRoomNumber("R201");
        newPetHotelStayDto.setStatus("RESERVED");
        newPetHotelStayDto.setPetId(1);

        ObjectMapper mapper = new ObjectMapper();
        mapper.findAndRegisterModules();
        String newPetHotelStayAsJSON = mapper.writeValueAsString(newPetHotelStayDto);

        this.mockMvc.perform(post("/api/pethotelstays")
                .content(newPetHotelStayAsJSON)
                .accept(MediaType.APPLICATION_JSON_VALUE)
                .contentType(MediaType.APPLICATION_JSON_VALUE))
            .andExpect(status().isCreated());
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testUpdatePetHotelStaySuccess() throws Exception {
        given(this.clinicService.findPetHotelStayById(1)).willReturn(petHotelStays.get(0));

        PetHotelStayFieldsDto updatedDto = new PetHotelStayFieldsDto();
        updatedDto.setCheckInDate(LocalDate.of(2023, 1, 5));
        updatedDto.setCheckOutDate(LocalDate.of(2023, 1, 10));
        updatedDto.setRoomNumber("R102");
        updatedDto.setStatus("CHECKED_OUT");
        updatedDto.setPetId(1);

        ObjectMapper mapper = new ObjectMapper();
        mapper.findAndRegisterModules();
        String updatedDtoAsJSON = mapper.writeValueAsString(updatedDto);

        this.mockMvc.perform(put("/api/pethotelstays/1")
                .content(updatedDtoAsJSON)
                .accept(MediaType.APPLICATION_JSON_VALUE)
                .contentType(MediaType.APPLICATION_JSON_VALUE))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.roomNumber").value("R102"));
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testUpdatePetHotelStayNotFound() throws Exception {
        given(this.clinicService.findPetHotelStayById(999)).willReturn(null);

        PetHotelStayFieldsDto updatedDto = new PetHotelStayFieldsDto();
        updatedDto.setCheckInDate(LocalDate.of(2023, 1, 5));
        updatedDto.setCheckOutDate(LocalDate.of(2023, 1, 10));
        updatedDto.setRoomNumber("R102");
        updatedDto.setStatus("CHECKED_OUT");
        updatedDto.setPetId(1);

        ObjectMapper mapper = new ObjectMapper();
        mapper.findAndRegisterModules();
        String updatedDtoAsJSON = mapper.writeValueAsString(updatedDto);

        this.mockMvc.perform(put("/api/pethotelstays/999")
                .content(updatedDtoAsJSON)
                .accept(MediaType.APPLICATION_JSON_VALUE)
                .contentType(MediaType.APPLICATION_JSON_VALUE))
            .andExpect(status().isNotFound());
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testDeletePetHotelStaySuccess() throws Exception {
        given(this.clinicService.findPetHotelStayById(1)).willReturn(petHotelStays.get(0));
        this.mockMvc.perform(delete("/api/pethotelstays/1")
                .accept(MediaType.APPLICATION_JSON))
            .andExpect(status().isNoContent());
        verify(this.clinicService).deletePetHotelStay(petHotelStays.get(0));
    }

    @Test
    @WithMockUser(roles = "OWNER_ADMIN")
    void testDeletePetHotelStayNotFound() throws Exception {
        given(this.clinicService.findPetHotelStayById(999)).willReturn(null);
        this.mockMvc.perform(delete("/api/pethotelstays/999")
                .accept(MediaType.APPLICATION_JSON))
            .andExpect(status().isNotFound());
    }
}
```

### 5.4 テスト実行とカバレッジレポート

```bash
# 新しいテストのみ実行
mvn test -Dtest=PetHotelStayRestControllerTests

# すべてのテスト実行
mvn test

# カバレッジレポート生成
mvn jacoco:report
```

### 5.5 カバレッジレポートの確認

**レポートの場所**:
- パス: `target/site/jacoco/index.html`
- ブラウザで開く: `file:///path/to/spring-petclinic-rest/target/site/jacoco/index.html`

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

**改善アクション**:
- カバレッジが目標に達していない場合、追加のテストケースを作成
- エッジケースや例外ケースのテストを追加

### 5.6 コミット
```bash
git add src/test/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestControllerTests.java
git commit -m "Add PetHotelStay controller tests"
```

---

## セクション6: 統合・総合テスト - QA エンジニア/QA マネージャーエージェント活用 (30分)

### 6.1 QA マネージャーエージェントの役割

QA マネージャーエージェントは以下をサポートします：
- 統合テストシナリオの設計
- エンドツーエンドテスト
- 品質基準の確認
- テスト報告書作成

### 6.2 アプリケーション起動とビルド確認

```
@qa-manager アプリケーションの統合テストを実施します。
以下の確認項目でテストシナリオを提案してください：

1. すべての単体テストがパスするか
2. アプリケーションが正常に起動するか
3. データベースマイグレーションが正常に動作するか
4. 全APIエンドポイントが正常に動作するか
5. 既存機能に影響がないか
```

### 6.3 ビルドとテスト実行

```bash
# 完全ビルド
mvn clean install

# アプリケーション起動
mvn spring-boot:run
```

### 6.4 API 統合テスト

QA マネージャーエージェントへのプロンプト：

```
@qa-manager 以下のAPIエンドポイントをcurlでテストするスクリプトを作成してください：

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

### 6.5 手動テスト実行

**テストケース1: すべての予約取得**
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays
```

**期待結果**: 200 OK、3件の予約データ

---

**テストケース2: 特定の予約取得**
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays/1
```

**期待結果**: 200 OK、id=1 の予約データ

---

**テストケース3: 新規予約作成**
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

**期待結果**: 201 CREATED、Location ヘッダー、作成された予約データ

---

**テストケース4: 予約更新**
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

**期待結果**: 200 OK、更新された予約データ

---

**テストケース5: 予約削除**
```bash
curl -u admin:admin -X DELETE http://localhost:9966/petclinic/api/pethotelstays/3
```

**期待結果**: 204 NO_CONTENT

---

**テストケース6: 既存機能への影響確認**
```bash
# Pet API が正常に動作するか確認
curl -u admin:admin http://localhost:9966/petclinic/api/pets

# Visit API が正常に動作するか確認
curl -u admin:admin http://localhost:9966/petclinic/api/visits
```

**期待結果**: 両方とも 200 OK、既存データが正常に取得できる

### 6.6 統合テスト時のカバレッジ確認

**統合テスト完了後、カバレッジレポートを再確認**:

```bash
# 完全なテストとカバレッジレポート生成
mvn clean test jacoco:report
```

**レポートの確認**:
- `target/site/jacoco/index.html` をブラウザで開く
- 統合テスト実行により、カバレッジが向上しているか確認
- エンドツーエンドでのカバレッジ状況を評価

**確認項目**:
1. 全体のカバレッジ率が目標値（Line: 85%, Branch: 66%）を達成しているか
2. 新規実装した PetHotelStay 関連クラスのカバレッジは十分か
3. 統合テストにより追加でカバーされた箇所はどこか

### 6.7 テスト報告書作成

```
@qa-manager 以下のテスト結果を基に、テスト報告書を作成してください：

【実施したテスト】
- 単体テスト: 8件すべてパス
- テストカバレッジ: Line 85%以上, Branch 66%以上達成
- ビルドテスト: 成功
- 統合テスト: 6つのAPIエンドポイントすべて正常動作
- 既存機能確認: Pet API, Visit API ともに正常動作

【不具合】
なし

【品質評価】
すべてのテストがパスし、カバレッジ目標を達成。既存機能に影響なし。リリース可能。
```

---

## セクション7: 受け入れとマージ - リードエージェント活用 (30分)

### 7.1 リードエージェントの役割

リードエージェントは以下をサポートします：
- Pull Request の品質確認
- コードレビュー観点の提示
- マージ判断
- ドキュメント確認

### 7.2 リモートへプッシュ

```bash
git push origin feature/pet-hotel
```

### 7.3 Pull Request 作成

リードエージェントへのプロンプト：

```
@lead 以下の変更内容に基づいて、Pull Request の説明文を作成してください：

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
- 既存機能に影響なし

【Issue】
Closes #1

適切なPR説明文とチェックリストを作成してください。
```

GitHub で Pull Request を作成:

**PRタイトル**: `ペットホテル機能の追加`

**PR説明** (リードエージェントが生成):

```markdown
## 概要
Closes #1

ペットを預かるペットホテル機能を実装しました。

## 変更内容

### データベース
- `pet_hotel_stays` テーブルを追加
- 外部キー制約とインデックスを設定
- サンプルデータを3件追加

### バックエンド
- `PetHotelStay` エンティティを追加
- `PetHotelStayRepository` インターフェースと実装を追加
- `ClinicService` にペットホテル関連メソッドを追加
- `PetHotelStayDto` と `PetHotelStayFieldsDto` を追加
- `PetHotelStayMapper` を追加（MapStruct）
- `PetHotelStayRestController` を追加

### テスト
- `PetHotelStayRestControllerTests` を追加
- 8件のテストケースを実装（すべてパス）

## 開発プロセス

各工程でGitHub Copilot カスタムエージェントを活用：
- 要求開発: PM エージェント
- 基本設計: アーキテクトエージェント
- 詳細設計: テックリードエージェント
- 実装: シニアデベロッパーエージェント
- 単体テスト: QA エンジニアエージェント
- 統合・総合テスト: QA エンジニア/マネージャーエージェント

## テスト結果
```bash
mvn test
# Tests run: 8, Failures: 0, Errors: 0, Skipped: 0
```

## 動作確認
- ✅ GET /api/pethotelstays - 予約一覧取得
- ✅ GET /api/pethotelstays/{id} - 特定予約取得
- ✅ POST /api/pethotelstays - 新規予約作成
- ✅ PUT /api/pethotelstays/{id} - 予約更新
- ✅ DELETE /api/pethotelstays/{id} - 予約削除

## チェックリスト
- [x] すべてのテストがパスする
- [x] 既存コードのパターンに従っている
- [x] 適切なバリデーションが実装されている
- [x] セキュリティ（@PreAuthorize）が設定されている
- [x] コミットメッセージが明確
- [x] 既存機能に影響がない
- [x] エージェント活用による品質確保

## レビュー観点
1. コード品質とスタイルの統一性
2. エラーハンドリングの適切性
3. セキュリティ設定
4. テストカバレッジ
5. データベース設計
```

### 7.4 コードレビュー

リードエージェントへのプロンプト：

```
@lead 以下のファイルをレビューしてください：

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

### 7.5 マージ

レビューが承認されたら、以下の手順でマージします:

1. GitHub PR画面で "Merge pull request" をクリック
2. **Squash and merge** を選択（推奨）
3. "Confirm merge" をクリック

```bash
# ローカルで master を更新
git checkout master
git pull origin master

# feature ブランチ削除
git branch -d feature/pet-hotel
```

### 7.6 振り返り

リードエージェントと振り返り：

```
@lead 今回のハンズオンを振り返ってください：

【良かった点】
【改善点】
【学んだこと】
【次のアクション】
```

**演習**:
1. 各工程でのエージェント活用の効果を振り返る
2. Issue Driven Development の理解度を確認
3. 次回への改善点を記録

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

## エージェント活用のまとめ

| 工程 | エージェント | 活用ポイント |
|------|------------|------------|
| 要求開発 | PM | ビジネス要件を技術要件に変換 |
| 基本設計 | アーキテクト | 既存パターンとの整合性確保 |
| 詳細設計 | テックリード | 実装可能な詳細設計 |
| 実装 | シニアデベロッパー | 高品質なコード生成 |
| 単体テスト | QA エンジニア | 包括的なテストケース |
| 統合・総合テスト | QA エンジニア/マネージャー | エンドツーエンド品質保証 |
| 受け入れ | リード | 全体品質確認とマージ判断 |

## 追加課題（時間がある場合）

### 課題1: ステータス管理の改善

```
@senior-dev `status` フィールドをStringからEnumに変更してください。

StayStatus Enum を作成し、PetHotelStay エンティティを更新してください。
```

### 課題2: バリデーション強化

```
@senior-dev 以下のバリデーションを実装してください：

1. チェックイン日がチェックアウト日より前であることを検証
2. 同じ部屋番号で期間が重複しないことを検証

カスタムバリデーターを作成してください。
```

### 課題3: 統計API追加

```
@techlead 現在の稼働状況を取得するAPIを設計・実装してください：

GET /api/pethotelstays/statistics
{
  "totalRooms": 10,
  "occupiedRooms": 7,
  "availableRooms": 3
}
```

### 課題4: ペット別の履歴取得

```
@techlead 特定のペットのホテル利用履歴を取得するAPIを追加してください：

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

**ハンズオン資料 エージェント活用版 バージョン 2.0**
