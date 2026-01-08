# 講師向けガイド

このガイドは、ペットホテル機能追加ハンズオンを指導する講師向けの資料です。

## ハンズオン概要

- **所要時間**: 300分（5時間）
- **対象者**: JavaとSpring Bootの基礎知識がある開発者
- **学習目標**: Issue Driven Development のワークフローを実践的に学ぶ
- **前提知識**:
  - Java 基礎
  - Spring Boot 基礎
  - Git 基本操作
  - REST API の概念

## 事前準備

### 1. 環境確認（開始1週間前）

参加者に以下をインストールしてもらう:
- Java 17 以上
- Maven 3.9 以上
- Git
- IDE (IntelliJ IDEA / Eclipse / VS Code)
- GitHub アカウント
- curl または Postman

### 2. リポジトリフォーク（開始3日前）

参加者に以下を依頼:
1. spring-petclinic-rest リポジトリをフォーク
2. ローカルにクローン
3. `mvn clean install` が成功することを確認
4. `mvn spring-boot:run` でアプリが起動することを確認

### 3. 講師側の準備

- [ ] サンプル実装を一通り完成させる
- [ ] 想定される質問への回答を準備
- [ ] デモ用の環境を準備
- [ ] スライド資料（必要に応じて）
- [ ] トラブルシューティング用のFAQ

## タイムテーブルと指導ポイント

### セクション1: 環境セットアップと既存機能の理解 (30分)

#### 0:00-0:15 導入とアプリケーション起動

**指導ポイント**:
- Issue Driven Development の重要性を説明
- 今日のゴールを明確にする
- アプリケーションが正常に起動することを全員で確認

**確認事項**:
```bash
# 全員がアプリを起動できているか
mvn spring-boot:run
# http://localhost:9966/petclinic にアクセスできるか
```

**よくある問題**:
- ポート9966が既に使用されている → ポート変更または既存プロセス終了
- Java バージョンが古い → Java 17以上をインストール

#### 0:15-0:30 既存コードの理解

**指導ポイント**:
- Visit エンティティを例に、コード構造を説明
- 3層アーキテクチャ（Controller, Service, Repository）を説明
- 実際にコードを開いて確認してもらう

**重要なファイル**:
1. `Visit.java` - シンプルなエンティティの例
2. `VisitRestController.java` - REST API の実装例
3. `ClinicServiceImpl.java` - サービス層の実装

**演習**:
参加者に curl でAPIを叩いてもらう
```bash
curl http://localhost:9966/petclinic/api/pets
```

---

### セクション2: Issue 作成とラベル管理 (30分)

#### 0:30-0:45 ラベル作成

**指導ポイント**:
- ラベルの重要性（フィルタリング、可視化）
- 一貫した命名規則の重要性

**デモ**:
講師が実際にラベルを作成する様子を画面共有で見せる

#### 0:45-1:00 Issue 作成

**指導ポイント**:
- 良いIssueの書き方
  - 明確なタイトル
  - 背景と目的
  - 受け入れ基準
  - タスクリスト

**演習**:
参加者が ISSUE_TEMPLATE.md を使ってIssueを作成

**確認**:
全員がIssueを作成し、適切なラベルを付与できているか巡回確認

---

### セクション3: 機能設計とブランチ作成 (30分)

#### 1:00-1:15 機能設計の説明

**指導ポイント**:
- 既存パターン（Visit）を踏襲する重要性
- エンティティ設計の考え方
- API設計の原則（RESTful）

**ホワイトボードで図示**:
```
Pet (1) --- (*) PetHotelStay
```

**設計の要点**:
- Visit と同じパターンを使う → 学習コストが低い
- 最小限の機能 → スコープクリープを防ぐ
- 既存コードとの整合性 → 保守性向上

#### 1:15-1:30 ブランチ作成と Git ワークフロー

**指導ポイント**:
- feature ブランチ戦略
- 命名規則の重要性
- コミット粒度

**演習**:
```bash
git checkout -b feature/pet-hotel
git branch  # 確認
```

**確認**:
全員が正しいブランチで作業しているか確認

---

### セクション4: 機能実装 (150分)

#### 1:30-1:50 データベーススキーマ追加 (20分)

**指導ポイント**:
- スキーマ設計の重要性
- 外部キー制約とインデックス
- テストデータの準備

**デモ**:
schema.sql と data.sql の編集を画面共有

**演習**:
参加者が実際にファイルを編集

**確認ポイント**:
```bash
git add src/main/resources/db/h2/
git commit -m "Add pet_hotel_stays table schema and test data"
```

**休憩タイミング**: ここで10分休憩（1:50-2:00）

---

#### 2:00-2:25 Entity 作成 (25分)

**指導ポイント**:
- JPA アノテーションの説明
- `@ManyToOne` 関係の重要性
- バリデーションアノテーション

**コード説明**:
- `extends BaseEntity` → ID管理を継承
- `@NotNull` → バリデーション
- `@JoinColumn` → 外部キー

**よくあるミス**:
- getter/setter の書き忘れ
- アノテーションのインポート間違い

**確認**:
```bash
git add src/main/java/org/springframework/samples/petclinic/model/PetHotelStay.java
git commit -m "Add PetHotelStay entity"
```

---

#### 2:25-2:45 Repository 作成 (20分)

**指導ポイント**:
- Spring Data JPA の利便性
- カスタムクエリメソッド
- プロファイル (`@Profile("spring-data-jpa")`)

**演習**:
2つのファイルを作成:
1. `PetHotelStayRepository.java`
2. `SpringDataPetHotelStayRepository.java`

**確認**:
コミットを確認

---

#### 2:45-3:10 Service 実装 (25分)

**指導ポイント**:
- トランザクション管理
- `@Transactional(readOnly = true)` の意味
- DIコンテナへの登録

**重要ポイント**:
1. インターフェースにメソッド追加
2. 実装クラスにフィールド追加
3. コンストラクタ更新
4. メソッド実装

**よくあるミス**:
- コンストラクタへの追加忘れ
- `@Transactional` の付け忘れ

**休憩タイミング**: ここで10分休憩（3:10-3:20）

---

#### 3:20-3:45 DTO と Mapper 作成 (25分)

**指導ポイント**:
- DTOの目的（エンティティの直接公開を避ける）
- MapStruct の自動生成
- `@Mapping` アノテーション

**演習**:
3つのファイルを作成:
1. `PetHotelStayDto.java`
2. `PetHotelStayFieldsDto.java`
3. `PetHotelStayMapper.java`

**確認**:
```bash
mvn clean compile
# MapStruct が正しく生成されるか確認
ls target/generated-sources/annotations/
```

---

#### 3:45-4:20 REST Controller 実装 (35分)

**指導ポイント**:
- `@RestController` の役割
- `@PreAuthorize` によるセキュリティ
- HTTPステータスコードの使い分け
- Location ヘッダー（POST時）

**重要なメソッド**:
- GET (一覧) → 200 OK
- GET (単体) → 200 OK / 404 NOT FOUND
- POST → 201 CREATED + Location header
- PUT → 200 OK / 404 NOT FOUND
- DELETE → 204 NO CONTENT / 404 NOT FOUND

**デモ**:
講師が1つのメソッドを実装して見せる

**演習**:
参加者がすべてのCRUDメソッドを実装

---

#### 4:20-4:45 テストコード作成 (25分)

**指導ポイント**:
- ユニットテストの重要性
- MockMvc の使い方
- `@WithMockUser` によるセキュリティテスト

**演習**:
`PetHotelStayRestControllerTests.java` を作成

**確認**:
```bash
mvn test -Dtest=PetHotelStayRestControllerTests
# すべてパスすることを確認
```

---

#### 4:45-5:05 動作確認 (20分)

**指導ポイント**:
- 実際のAPIを叩いて動作確認
- curl の使い方
- レスポンスの確認

**演習**:
参加者が curl で全エンドポイントをテスト

**確認事項**:
- ✅ GET 一覧
- ✅ GET 単体
- ✅ POST 新規作成
- ✅ PUT 更新
- ✅ DELETE 削除

**休憩タイミング**: ここで10分休憩（5:05-5:15）

---

### セクション5: Pull Request 作成とレビュー (30分)

#### 5:15-5:30 PR作成

**指導ポイント**:
- 良いPRの書き方
  - 明確なタイトル
  - Issue参照 (`Closes #1`)
  - 変更内容の説明
  - テスト結果
  - チェックリスト

**演習**:
1. リモートにプッシュ
2. GitHub でPR作成
3. PR_TEMPLATE.md を活用

#### 5:30-5:45 コードレビュー

**指導ポイント**:
- レビューの観点
- 建設的なフィードバック
- レビューコメントの書き方

**演習**:
参加者同士でペアを組んでレビュー

**レビュー観点**:
1. コード品質
2. 機能性
3. テスト
4. セキュリティ
5. データベース設計

---

### セクション6: マージと振り返り (30分)

#### 5:45-6:00 マージ

**指導ポイント**:
- マージ方法の違い
  - Merge commit
  - Squash and merge (推奨)
  - Rebase and merge

**演習**:
レビュー承認後にマージ

#### 6:00-6:15 振り返り

**ディスカッション**:
- 学んだことは？
- 難しかった点は？
- 実務で活かせそうなことは？

**フィードバック収集**:
- ハンズオンの難易度
- 時間配分
- 改善点

#### 6:15-6:30 追加課題の紹介と質疑応答

**追加課題の説明**:
- ステータス管理の改善（Enum化）
- バリデーション強化
- 統計API追加
- ペット別履歴取得

**質疑応答**:
参加者からの質問に回答

---

## よくある質問と回答

### Q1: MapStruct のコンパイルエラーが出る

**A**: 以下を実行:
```bash
mvn clean install -DskipTests
```
それでもダメな場合は、IDEのキャッシュクリアと再起動。

### Q2: テストが失敗する

**A**: 以下を確認:
1. MapStruct の生成クラスが存在するか
2. モックの設定が正しいか
3. インポートが正しいか

### Q3: API が 401 を返す

**A**: 認証情報を確認:
```bash
curl -u admin:admin http://localhost:9966/petclinic/api/pethotelstays
```

### Q4: データベーススキーマが反映されない

**A**: アプリケーションを再起動（H2はインメモリDB）

### Q5: ブランチを間違えてコミットした

**A**:
```bash
# 新しいブランチ作成
git checkout -b feature/pet-hotel
# 元のブランチに戻る
git checkout master
# 最新のコミットを取り消す（ファイルは残る）
git reset --soft HEAD~1
# 正しいブランチに切り替え
git checkout feature/pet-hotel
```

---

## トラブルシューティング

### ポート衝突
```bash
# ポート確認
lsof -i :9966
# プロセス終了
kill -9 <PID>
```

### Maven ビルド失敗
```bash
# キャッシュクリア
mvn clean
rm -rf ~/.m2/repository/org/springframework/samples/petclinic
mvn install
```

### Git コンフリクト
```bash
# master の最新を取得
git checkout master
git pull origin master
# feature ブランチにマージ
git checkout feature/pet-hotel
git merge master
# コンフリクト解決後
git add .
git commit
```

---

## 評価基準

### 優れている（A）
- すべての機能が動作する
- テストが全てパスする
- コードが既存パターンに従っている
- コミットメッセージが明確
- PRの説明が詳細

### 良好（B）
- 主要機能が動作する
- ほとんどのテストがパスする
- 基本的なパターンに従っている
- 最低限のドキュメント

### 要改善（C）
- 一部の機能のみ動作
- テストが不十分
- コードの一貫性が低い

---

## 次回への改善ポイント

ハンズオン終了後に記録:
- [ ] 時間配分は適切だったか？
- [ ] 難易度は適切だったか？
- [ ] 資料は分かりやすかったか？
- [ ] 追加すべき内容は？
- [ ] 削除すべき内容は？

---

## 参考資料

- [Spring PetClinic REST](https://github.com/spring-petclinic/spring-petclinic-rest)
- [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Effective Code Reviews](https://google.github.io/eng-practices/review/)

---

**講師向けガイド バージョン 1.0**
