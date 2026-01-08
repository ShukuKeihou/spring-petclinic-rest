# Pull Request テンプレート

このテンプレートをコピーして、Pull Request を作成する際に使用してください。

## ペットホテル機能の追加

### 概要
Closes #1

ペットを預かるペットホテル機能を実装しました。

### 変更内容

#### データベース
- `pet_hotel_stays` テーブルを追加
- サンプルデータを追加

#### バックエンド
- `PetHotelStay` エンティティを追加
- `PetHotelStayRepository` インターフェースと実装を追加
- `ClinicService` にペットホテル関連メソッドを追加
- `PetHotelStayDto` と `PetHotelStayFieldsDto` を追加
- `PetHotelStayMapper` を追加
- `PetHotelStayRestController` を追加

#### テスト
- `PetHotelStayRestControllerTests` を追加
- すべてのCRUD操作のテストケースを実装

### テスト結果
```bash
mvn test
# すべてのテストがパス
```

### 動作確認
- ✅ GET /api/pethotelstays - 予約一覧取得
- ✅ GET /api/pethotelstays/{id} - 特定予約取得
- ✅ POST /api/pethotelstays - 新規予約作成
- ✅ PUT /api/pethotelstays/{id} - 予約更新
- ✅ DELETE /api/pethotelstays/{id} - 予約削除

### チェックリスト
- [ ] すべてのテストがパスする
- [ ] 既存コードのパターンに従っている
- [ ] 適切なバリデーションが実装されている
- [ ] セキュリティ（@PreAuthorize）が設定されている
- [ ] コミットメッセージが明確
- [ ] 既存機能に影響がない

### レビュー観点
以下の点を重点的にレビューしてください：
1. コード品質とスタイルの統一性
2. エラーハンドリングの適切性
3. セキュリティ設定
4. テストカバレッジ
5. データベース設計

### スクリーンショット
（APIテストの実行結果や動作確認のスクリーンショットを添付してください）
