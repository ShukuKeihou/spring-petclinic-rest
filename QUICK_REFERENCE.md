# クイックリファレンス

ハンズオン中に頻繁に使用するコマンドやパスをまとめたチートシートです。

## Git コマンド

### 基本操作
```bash
# ブランチ作成と切り替え
git checkout -b feature/pet-hotel

# 変更の確認
git status
git diff

# ステージング
git add <file>
git add .

# コミット
git commit -m "メッセージ"

# プッシュ
git push origin feature/pet-hotel

# ブランチ確認
git branch
```

### ブランチ管理
```bash
# masterを最新化
git checkout master
git pull origin master

# feature ブランチに戻る
git checkout feature/pet-hotel

# ブランチ削除（マージ後）
git branch -d feature/pet-hotel
git push origin --delete feature/pet-hotel
```

## Maven コマンド

```bash
# ビルド
mvn clean install

# テスト実行
mvn test

# 特定のテストのみ実行
mvn test -Dtest=PetHotelStayRestControllerTests

# アプリケーション起動
mvn spring-boot:run

# テストスキップでビルド
mvn clean install -DskipTests
```

## curl コマンド（API テスト）

### 認証情報
- ユーザー名: `admin`
- パスワード: `admin`

### API エンドポイント

**全予約取得**:
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays
```

**特定予約取得**:
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

## ファイルパス

### モデル（Entity）
```
src/main/java/org/springframework/samples/petclinic/model/
├── PetHotelStay.java (新規作成)
├── Pet.java (参考)
└── Visit.java (参考)
```

### リポジトリ
```
src/main/java/org/springframework/samples/petclinic/repository/
├── PetHotelStayRepository.java (新規作成)
└── springdatajpa/
    └── SpringDataPetHotelStayRepository.java (新規作成)
```

### サービス
```
src/main/java/org/springframework/samples/petclinic/service/
├── ClinicService.java (更新)
└── ClinicServiceImpl.java (更新)
```

### REST コントローラ
```
src/main/java/org/springframework/samples/petclinic/rest/controller/
├── PetHotelStayRestController.java (新規作成)
└── VisitRestController.java (参考)
```

### DTO
```
src/main/java/org/springframework/samples/petclinic/rest/dto/
├── PetHotelStayDto.java (新規作成)
└── PetHotelStayFieldsDto.java (新規作成)
```

### マッパー
```
src/main/java/org/springframework/samples/petclinic/mapper/
├── PetHotelStayMapper.java (新規作成)
└── VisitMapper.java (参考)
```

### テスト
```
src/test/java/org/springframework/samples/petclinic/rest/controller/
└── PetHotelStayRestControllerTests.java (新規作成)
```

### データベース
```
src/main/resources/db/h2/
├── schema.sql (更新)
└── data.sql (更新)
```

## コード スニペット

### Entity 基本構造
```java
@Entity
@Table(name = "pet_hotel_stays")
public class PetHotelStay extends BaseEntity {
    @Column(name = "check_in_date")
    @NotNull
    private LocalDate checkInDate;

    @ManyToOne
    @JoinColumn(name = "pet_id")
    @NotNull
    private Pet pet;

    // getters and setters
}
```

### Repository インターフェース
```java
public interface PetHotelStayRepository {
    PetHotelStay findById(int id) throws DataAccessException;
    Collection<PetHotelStay> findAll() throws DataAccessException;
    void save(PetHotelStay petHotelStay) throws DataAccessException;
    void delete(PetHotelStay petHotelStay) throws DataAccessException;
}
```

### Controller メソッド例
```java
@PreAuthorize("hasRole(@roles.OWNER_ADMIN)")
@GetMapping("/{petHotelStayId}")
public ResponseEntity<PetHotelStayDto> getPetHotelStay(
        @PathVariable("petHotelStayId") int petHotelStayId) {
    PetHotelStay petHotelStay = clinicService.findPetHotelStayById(petHotelStayId);
    if (petHotelStay == null) {
        return new ResponseEntity<>(HttpStatus.NOT_FOUND);
    }
    return new ResponseEntity<>(petHotelStayMapper.toPetHotelStayDto(petHotelStay), HttpStatus.OK);
}
```

### Mapper インターフェース
```java
@Mapper(uses = PetMapper.class)
public interface PetHotelStayMapper {
    @Mapping(source = "pet.id", target = "petId")
    PetHotelStayDto toPetHotelStayDto(PetHotelStay petHotelStay);

    @Mapping(source = "petId", target = "pet.id")
    PetHotelStay toPetHotelStay(PetHotelStayDto petHotelStayDto);
}
```

## データベーススキーマ

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
```

## テストデータ

```sql
INSERT INTO pet_hotel_stays VALUES (1, 7, '2023-01-05', '2023-01-10', 'R101', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (2, 8, '2023-02-15', '2023-02-20', 'R102', 'CHECKED_OUT');
INSERT INTO pet_hotel_stays VALUES (3, 8, '2023-12-20', '2023-12-27', 'R103', 'RESERVED');
```

## URL

- アプリケーション: http://localhost:9966/petclinic
- Swagger UI: http://localhost:9966/petclinic/swagger-ui.html
- H2 Console: http://localhost:9966/petclinic/h2-console

## トラブルシューティング

### MapStruct のコンパイルエラー
```bash
mvn clean install -DskipTests
```

### ポートが既に使用されている
```bash
# プロセス確認
lsof -i :9966
# または
netstat -an | grep 9966

# プロセス終了
kill -9 <PID>
```

### データベースリセット
アプリケーションを再起動すると、H2データベースは自動的にリセットされます。

## 参考リンク

- [Spring PetClinic REST GitHub](https://github.com/spring-petclinic/spring-petclinic-rest)
- [Spring Boot Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
