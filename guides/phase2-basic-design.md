# Phase 2: 基本設計

**担当エージェント**: ソフトウェアアーキテクト

**目標**:
- システムアーキテクチャを設計する
- エンティティモデルを定義する
- 既存パターンとの整合性を確認する
- 設計ドキュメントを作成してPRを出す

**前提条件**: Phase 1 (Issue #1) がマージ済みであること

---

## 2.1 Phase 2 Issue の作成

Phase 1（要求開発）が完了したので、Phase 2（基本設計）の Issue を作成します。

**Issue Form**: `.github/ISSUE_TEMPLATE/02_basic_design.yml`

**Issue タイトル**: `[基本設計] ペットホテル機能のシステムアーキテクチャとエンティティ設計`

**前工程 Issue**: Phase 1 の Issue 番号を記入（例: `#1`）

**Issue 内容のポイント**:
- Phase 1 で定義した要件を基に、システム構造を設計
- エンティティモデルを定義
- 既存パターンとの整合性を確認

---

## 2.2 アーキテクトエージェントの役割

ソフトウェアアーキテクトエージェントは以下をサポートします：
- システムアーキテクチャの設計
- エンティティモデルの設計
- 既存パターンとの整合性確認
- 技術選択の推奨

---

## 2.3 アーキテクトエージェントへのプロンプト例

```
以下の既存コードベースを分析して、ペットホテル機能の基本設計を行ってください：

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

---

## 2.4 エンティティ設計

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

---

## 2.5 アーキテクチャ確認

アーキテクトエージェントと以下を確認：

```
以下の点について確認してください：

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

## 2.6 Phase 2 の Pull Request 作成と承認

### 設計ドキュメント作成

基本設計の内容をドキュメント化します：

```bash
# Phase 2 用のブランチ作成
git checkout -b feature/phase2-basic-design

# 設計ドキュメントを作成
# docs/phase2-basic-design.md

# コミット
git add docs/phase2-basic-design.md
git commit -m "Phase 2: Add basic design document

Related Issue: #2

- Define PetHotelStay entity structure
- Define relationships with Pet entity
- Define JPA annotations
- Confirm consistency with existing patterns"

git push origin feature/phase2-basic-design
```

### Pull Request 作成

**PRタイトル**: `Phase 2: ペットホテル機能の基本設計 (#2)`

**PR説明**:
```markdown
## Phase 2: 基本設計

Closes #2
Depends on: #1

## 概要
ペットホテル機能のシステムアーキテクチャとエンティティモデルを設計しました。

## 変更内容
- ✅ 基本設計書を作成 (`docs/phase2-basic-design.md`)
- ✅ PetHotelStay エンティティ構造を定義
- ✅ Pet との関係を設計（@ManyToOne）
- ✅ JPA アノテーションを定義
- ✅ ER図を作成
- ✅ 既存パターンとの整合性を確認

## エージェント活用
- Architect エージェント (`@architect`) を活用して設計

## 設計の要点
1. **Visitパターンを踏襲**: 既存コードとの一貫性
2. **BaseEntity継承**: ID管理の統一
3. **LocalDate使用**: 日付のみを扱う
4. **FetchType.LAZY**: パフォーマンス最適化

## チェックリスト
- [x] エンティティモデルが明確に定義されている
- [x] データベーススキーマが設計されている
- [x] レイヤー構造が明確
- [x] 既存パターンとの一貫性が確保されている
- [x] 次工程（Phase 3: 詳細設計）への引き継ぎ情報が明確

## レビュー観点
1. エンティティ設計の妥当性
2. 既存パターンとの整合性
3. データモデルの正規化
4. パフォーマンスへの配慮

## 次のステップ
このPRがマージされたら、Phase 3（詳細設計）のIssue #3を作成してください。
```

**ラベル**: `phase:basic-design`, `status:review`

### マージとクリーンアップ

```bash
# GitHubでマージ後
git checkout master
git pull origin master
git branch -d feature/phase2-basic-design
```

---

## 完了チェックリスト

Phase 2 が完了したら、以下を確認してください：

- [ ] Issue #2 が作成されている
- [ ] 基本設計書 (`docs/phase2-basic-design.md`) が作成されている
- [ ] PR #2 が作成され、マージされている
- [ ] Issue #2 がクローズされている
- [ ] feature/phase2-basic-design ブランチが削除されている

---

## トラブルシューティング

### エンティティ設計で迷う

**解決策**:
- Visit.java を参考にパターンを確認してください
- BaseEntity の継承方法を確認してください
- アーキテクトエージェントに既存パターンとの比較を依頼してください

### ER図の作成方法がわからない

**解決策**:
- Mermaid diagram を使用すると簡単に作成できます
- アーキテクトエージェントに Mermaid 形式での ER図生成を依頼してください

---

## 次のステップ

**Phase 2 が完了したら、[Phase 3: 詳細設計](phase3-detailed-design.md)に進んでください。**

---

[メインガイドに戻る](../HANDS_ON_GUIDE.md)
