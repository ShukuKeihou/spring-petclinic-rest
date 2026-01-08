# ペットホテル機能追加ハンズオン

## 概要

このハンズオンでは、Spring PetClinic REST アプリケーションに「ペットホテル機能」を追加することで、Issue Driven Development のワークフローを学びます。

**所要時間**: 約300分（5時間）

**学習目標**:
- Issue の作成とラベル管理
- ブランチ戦略とGitワークフロー
- Pull Request の作成とコードレビュー
- マージとクローズの一連の流れ

## 前提条件

- Java 17 以上
- Maven 3.9 以上
- Git
- GitHub アカウント
- IDE (IntelliJ IDEA, Eclipse, VS Code など)

## タイムテーブル

| 時間 | 内容 |
|------|------|
| 0:00-0:30 | セクション1: 環境セットアップと既存機能の理解 |
| 0:30-1:00 | セクション2: Issue 作成とラベル管理 |
| 1:00-1:30 | セクション3: 機能設計とブランチ作成 |
| 1:30-4:00 | セクション4: 機能実装 |
| 4:00-4:30 | セクション5: Pull Request 作成とレビュー |
| 4:30-5:00 | セクション6: マージと振り返り |

---

## セクション1: 環境セットアップと既存機能の理解 (30分)

### 1.1 リポジトリのフォークとクローン

1. GitHub で spring-petclinic-rest リポジトリをフォーク
2. ローカルにクローン:

```bash
git clone https://github.com/YOUR_USERNAME/spring-petclinic-rest.git
cd spring-petclinic-rest
```

### 1.2 アプリケーションの起動

```bash
# ビルドとテスト実行
mvn clean install

# アプリケーション起動
mvn spring-boot:run
```

アプリケーションは `http://localhost:9966` で起動します。

### 1.3 既存 API の確認

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

### 1.4 既存コードの構造理解

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

## セクション2: Issue 作成とラベル管理 (30分)

### 2.1 GitHub Labels の作成

まず、プロジェクトで使用するラベルを作成します。

GitHub リポジトリの **Issues** タブ → **Labels** に移動し、以下のラベルを作成:

| ラベル名 | 色 | 説明 |
|---------|-----|------|
| `enhancement` | #a2eeef | 新機能追加 |
| `feature` | #0075ca | フィーチャー実装 |
| `backend` | #d93f0b | バックエンド関連 |
| `database` | #fbca04 | データベース変更 |
| `good first issue` | #7057ff | 初心者向け |

### 2.2 メインIssueの作成

**Issue タイトル**: `ペットホテル機能の追加`

**Issue 本文テンプレート**:

```markdown
## 概要
ペットを預かるペットホテル機能を実装する。

## 背景
クリニックを訪れる飼い主から、旅行や出張中にペットを預かってほしいという要望が多く寄せられている。

## 要件

### 機能要件
- ペットホテルの予約情報を管理できる
- チェックイン日とチェックアウト日を記録できる
- 部屋番号を管理できる
- ステータス管理（予約済み、チェックイン済み、チェックアウト済み）
- REST APIで予約のCRUD操作ができる

### 非機能要件
- 既存のコード構造とパターンに従う
- 適切なテストを含める
- OpenAPI仕様に準拠する

## 実装タスク

- [ ] データベーススキーマ追加
- [ ] PetHotelStay エンティティ作成
- [ ] Repository インターフェース作成
- [ ] Service メソッド追加
- [ ] DTO と Mapper 作成
- [ ] REST Controller 実装
- [ ] テストコード作成
- [ ] OpenAPI仕様更新

## 技術的考慮事項
- Visit エンティティのパターンを参考にする
- 既存の Pet との関連付けを活用する
- Spring Data JPA を使用する
- MapStruct でDTO変換を行う

## 受け入れ基準
- [ ] すべてのテストがパスする
- [ ] API経由でペットホテル予約のCRUD操作ができる
- [ ] 既存機能に影響を与えない
- [ ] コードレビューで承認される
```

**演習**: 上記の内容で Issue を作成し、以下のラベルを付与してください:
- `enhancement`
- `feature`
- `backend`
- `database`

### 2.3 タスク分割（オプション）

大きなIssueを小さなタスクに分割することも可能です。例:
- Issue #2: データベーススキーマとエンティティ作成
- Issue #3: Repository と Service 実装
- Issue #4: REST API 実装

今回のハンズオンでは、1つの Issue で進めます。

---

## セクション3: 機能設計とブランチ作成 (30分)

### 3.1 機能設計

ペットホテル機能の設計を行います。既存の `Visit` パターンを参考にします。

#### 3.1.1 エンティティ設計

**PetHotelStay エンティティ**:

```java
@Entity
@Table(name = "pet_hotel_stays")
public class PetHotelStay extends BaseEntity {

    @Column(name = "check_in_date")
    private LocalDate checkInDate;

    @Column(name = "check_out_date")
    private LocalDate checkOutDate;

    @Column(name = "room_number")
    private String roomNumber;

    @Column(name = "status")
    private String status; // RESERVED, CHECKED_IN, CHECKED_OUT

    @ManyToOne
    @JoinColumn(name = "pet_id")
    private Pet pet;
}
```

#### 3.1.2 データベーススキーマ

```sql
CREATE TABLE pet_hotel_stays (
    id INTEGER IDENTITY PRIMARY KEY,
    pet_id INTEGER NOT NULL,
    check_in_date DATE,
    check_out_date DATE,
    room_number VARCHAR(10),
    status VARCHAR(20)
);

ALTER TABLE pet_hotel_stays
    ADD CONSTRAINT fk_pet_hotel_stay_pet
    FOREIGN KEY (pet_id) REFERENCES pets(id);
```

#### 3.1.3 REST API設計

| メソッド | エンドポイント | 説明 |
|---------|---------------|------|
| GET | /api/pethotelstays | すべての予約一覧 |
| GET | /api/pethotelstays/{id} | 特定の予約取得 |
| POST | /api/pethotelstays | 新規予約作成 |
| PUT | /api/pethotelstays/{id} | 予約更新 |
| DELETE | /api/pethotelstays/{id} | 予約削除 |

### 3.2 ブランチ作成

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

**命名規則**: `feature/` + `issue番号または機能名`

---

## セクション4: 機能実装 (150分)

### 4.1 データベーススキーマ追加 (20分)

#### ステップ1: H2スキーマファイル編集

`src/main/resources/db/h2/schema.sql` を編集し、以下を追加:

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

`src/main/resources/db/h2/data.sql` を編集し、サンプルデータを追加:

```sql
INSERT INTO pet_hotel_stays VALUES (1, 7, '2023-01-05', '2023-01-10', 'R101', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (2, 8, '2023-02-15', '2023-02-20', 'R102', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (3, 8, '2023-12-20', '2023-12-27', 'R103', 'RESERVED');
```

#### ステップ3: コミット

```bash
git add src/main/resources/db/h2/schema.sql src/main/resources/db/h2/data.sql
git commit -m "Add pet_hotel_stays table schema and test data"
```

### 4.2 Entity 作成 (25分)

#### ステップ1: PetHotelStay エンティティ作成

`src/main/java/org/springframework/samples/petclinic/model/PetHotelStay.java` を作成:

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

    // Getters and Setters
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

#### ステップ2: コミット

```bash
git add src/main/java/org/springframework/samples/petclinic/model/PetHotelStay.java
git commit -m "Add PetHotelStay entity"
```

### 4.3 Repository 作成 (20分)

#### ステップ1: Repository インターフェース作成

`src/main/java/org/springframework/samples/petclinic/repository/PetHotelStayRepository.java` を作成:

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

#### ステップ2: Spring Data JPA 実装

`src/main/java/org/springframework/samples/petclinic/repository/springdatajpa/SpringDataPetHotelStayRepository.java` を作成:

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

#### ステップ3: コミット

```bash
git add src/main/java/org/springframework/samples/petclinic/repository/
git commit -m "Add PetHotelStay repository"
```

### 4.4 Service 実装 (25分)

#### ステップ1: ClinicService インターフェース更新

`src/main/java/org/springframework/samples/petclinic/service/ClinicService.java` に以下のメソッドを追加:

```java
// PetHotelStay関連メソッド
PetHotelStay findPetHotelStayById(int id) throws DataAccessException;

Collection<PetHotelStay> findAllPetHotelStays() throws DataAccessException;

void savePetHotelStay(PetHotelStay petHotelStay) throws DataAccessException;

void deletePetHotelStay(PetHotelStay petHotelStay) throws DataAccessException;

Collection<PetHotelStay> findPetHotelStaysByPetId(Integer petId) throws DataAccessException;
```

#### ステップ2: ClinicServiceImpl 実装更新

`src/main/java/org/springframework/samples/petclinic/service/ClinicServiceImpl.java` を更新:

1. PetHotelStayRepository をフィールドに追加:

```java
private final PetHotelStayRepository petHotelStayRepository;
```

2. コンストラクタに追加:

```java
public ClinicServiceImpl(
    // ... 既存のパラメータ
    PetHotelStayRepository petHotelStayRepository) {
    // ... 既存の初期化
    this.petHotelStayRepository = petHotelStayRepository;
}
```

3. メソッド実装を追加:

```java
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

#### ステップ3: コミット

```bash
git add src/main/java/org/springframework/samples/petclinic/service/
git commit -m "Add PetHotelStay service methods"
```

### 4.5 DTO と Mapper 作成 (25分)

#### ステップ1: DTO クラス作成

`src/main/java/org/springframework/samples/petclinic/rest/dto/PetHotelStayDto.java` を作成:

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

`src/main/java/org/springframework/samples/petclinic/rest/dto/PetHotelStayFieldsDto.java` を作成:

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

#### ステップ2: Mapper 作成

`src/main/java/org/springframework/samples/petclinic/mapper/PetHotelStayMapper.java` を作成:

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

#### ステップ3: コミット

```bash
git add src/main/java/org/springframework/samples/petclinic/rest/dto/ src/main/java/org/springframework/samples/petclinic/mapper/
git commit -m "Add PetHotelStay DTOs and Mapper"
```

### 4.6 REST Controller 実装 (35分)

#### ステップ1: Controller クラス作成

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

#### ステップ2: コミット

```bash
git add src/main/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestController.java
git commit -m "Add PetHotelStay REST controller"
```

### 4.7 テストコード作成 (25分)

#### ステップ1: Controller テスト作成

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
import org.springframework.samples.petclinic.rest.dto.PetHotelStayDto;
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

#### ステップ2: テスト実行

```bash
# 新しいテストのみ実行
mvn test -Dtest=PetHotelStayRestControllerTests

# すべてのテスト実行
mvn test
```

#### ステップ3: コミット

```bash
git add src/test/java/org/springframework/samples/petclinic/rest/controller/PetHotelStayRestControllerTests.java
git commit -m "Add PetHotelStay controller tests"
```

### 4.8 動作確認 (20分)

#### ステップ1: アプリケーション起動

```bash
mvn spring-boot:run
```

#### ステップ2: API テスト

**すべての予約取得**:
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays
```

**特定の予約取得**:
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays/1
```

**新規予約作成**:
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

**予約更新**:
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

**予約削除**:
```bash
curl -u admin:admin -X DELETE http://localhost:9966/petclinic/api/pethotelstays/3
```

---

## セクション5: Pull Request 作成とレビュー (30分)

### 5.1 最終確認

#### ステップ1: すべてのテスト実行

```bash
mvn clean test
```

すべてのテストがパスすることを確認してください。

#### ステップ2: コミット履歴の確認

```bash
git log --oneline
```

適切なコミットメッセージになっているか確認してください。

### 5.2 リモートへプッシュ

```bash
git push origin feature/pet-hotel
```

### 5.3 Pull Request 作成

#### ステップ1: GitHub でPR作成

1. GitHub リポジトリページに移動
2. "Compare & pull request" ボタンをクリック
3. 以下の内容でPRを作成:

**PRタイトル**: `ペットホテル機能の追加`

**PR説明**:

```markdown
## 概要
Closes #1

ペットを預かるペットホテル機能を実装しました。

## 変更内容

### データベース
- `pet_hotel_stays` テーブルを追加
- サンプルデータを追加

### バックエンド
- `PetHotelStay` エンティティを追加
- `PetHotelStayRepository` インターフェースと実装を追加
- `ClinicService` にペットホテル関連メソッドを追加
- `PetHotelStayDto` と `PetHotelStayFieldsDto` を追加
- `PetHotelStayMapper` を追加
- `PetHotelStayRestController` を追加

### テスト
- `PetHotelStayRestControllerTests` を追加
- すべてのCRUD操作のテストケースを実装

## テスト結果
```bash
mvn test
# すべてのテストがパス
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

## スクリーンショット
（APIテストの実行結果や動作確認のスクリーンショットを追加）
```

### 5.4 コードレビュー

#### レビューポイント

レビュアーは以下の点を確認してください:

1. **コード品質**
   - [ ] 既存のコーディングスタイルに従っているか
   - [ ] 命名規則が適切か
   - [ ] 不要なコードがないか

2. **機能性**
   - [ ] すべてのCRUD操作が実装されているか
   - [ ] エラーハンドリングが適切か
   - [ ] バリデーションが適切か

3. **テスト**
   - [ ] テストカバレッジが十分か
   - [ ] エッジケースがテストされているか
   - [ ] テストが実際にパスするか

4. **セキュリティ**
   - [ ] 認証・認可が適切に設定されているか
   - [ ] SQLインジェクション対策ができているか

5. **データベース**
   - [ ] スキーマ設計が適切か
   - [ ] インデックスが適切に設定されているか
   - [ ] 外部キー制約が適切か

#### レビューコメントの例

```markdown
## 全体的な評価
実装パターンが既存コードに統一されており、良好です。

## 詳細コメント

### PetHotelStay.java:15
`status` フィールドをStringではなくEnumにすることを検討してください。
これにより、タイプセーフティが向上します。

```java
public enum StayStatus {
    RESERVED, CHECKED_IN, CHECKED_OUT
}
```

### PetHotelStayRestController.java:45
`checkInDate` が `checkOutDate` より後の日付でないかのバリデーションを追加することをお勧めします。

### 良い点
- テストカバレッジが十分
- エラーハンドリングが適切
- セキュリティ設定が正しい
```

#### ステップ2: レビューへの対応

レビューコメントを受けて修正が必要な場合:

```bash
# 修正を実施
git add .
git commit -m "Apply review feedback: Add validation for check-in/out dates"
git push origin feature/pet-hotel
```

PRは自動的に更新されます。

---

## セクション6: マージと振り返り (30分)

### 6.1 マージ

#### ステップ1: レビュー承認後

レビューが承認されたら、以下の手順でマージします:

1. GitHub PR画面で "Merge pull request" をクリック
2. マージ方法を選択:
   - **Merge commit**: すべてのコミット履歴を保持
   - **Squash and merge**: 1つのコミットにまとめる（推奨）
   - **Rebase and merge**: リベースしてマージ

3. "Confirm merge" をクリック

#### ステップ2: ブランチ削除

```bash
# リモートブランチ削除（GitHubで自動削除も可能）
git push origin --delete feature/pet-hotel

# ローカルブランチ削除
git checkout master
git pull origin master
git branch -d feature/pet-hotel
```

#### ステップ3: Issue クローズ

PR がマージされると、関連する Issue が自動的にクローズされます（PR説明に `Closes #1` と記載した場合）。

### 6.2 振り返り

#### 学習した内容の確認

1. **Issue Driven Development**
   - Issue で要件を明確化
   - ラベルでカテゴリ分類
   - タスクを細分化

2. **Git ワークフロー**
   - feature ブランチの作成
   - 小さなコミットの積み重ね
   - リモートへのプッシュ

3. **Pull Request プロセス**
   - 詳細な説明の記載
   - レビュアーへの配慮
   - フィードバックへの対応

4. **コードレビュー**
   - 品質の観点
   - セキュリティの観点
   - 保守性の観点

5. **Spring Boot 開発**
   - エンティティ設計
   - Repository パターン
   - Service レイヤー
   - REST Controller
   - DTO と Mapper

#### 改善点の議論

- より効率的なワークフローはあったか？
- コミット粒度は適切だったか？
- テストは十分だったか？
- ドキュメントは必要か？

---

## 追加課題（時間がある場合）

### 課題1: ステータス管理の改善

`status` フィールドを Enum に変更してタイプセーフティを向上させる。

### 課題2: バリデーション強化

- チェックイン日がチェックアウト日より前であることを検証
- 同じ部屋番号で期間が重複しないことを検証

### 課題3: 統計API追加

現在の稼働状況を取得するAPIを追加:
```
GET /api/pethotelstays/statistics
{
  "totalRooms": 10,
  "occupiedRooms": 7,
  "availableRooms": 3
}
```

### 課題4: ペット別の履歴取得

特定のペットのホテル利用履歴を取得するAPIを追加:
```
GET /api/pets/{petId}/hotelstays
```

---

## まとめ

このハンズオンを通じて、以下のスキルを習得しました:

✅ Issue Driven Development の実践
✅ Git のブランチ戦略
✅ Pull Request の作成と管理
✅ コードレビューのプロセス
✅ Spring Boot での REST API 開発
✅ テスト駆動開発の基礎

次のステップとして、実際のプロジェクトでこのワークフローを実践してみてください。

---

## 参考資料

- [Spring PetClinic REST](https://github.com/spring-petclinic/spring-petclinic-rest)
- [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Code Review Best Practices](https://google.github.io/eng-practices/review/)

---

## トラブルシューティング

### Q1: テストが失敗する

**A**: 以下を確認してください:
- MapStruct の生成クラスがビルドされているか（`mvn clean compile`）
- データベーススキーマが正しく適用されているか
- モックの設定が適切か

### Q2: API が 401 エラーを返す

**A**: 認証情報を確認してください:
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays
```

### Q3: PR がマージできない

**A**: 以下を確認してください:
- すべてのテストがパスしているか
- コンフリクトが発生していないか
- 必要な承認を得ているか

### Q4: MapStruct のコンパイルエラー

**A**: 以下を実行してください:
```bash
mvn clean install -DskipTests
```

---

**ハンズオン資料 バージョン 1.0**
