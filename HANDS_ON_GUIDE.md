# ペットホテル機能追加ハンズオン - エージェント活用版

## 概要

このハンズオンでは、Spring PetClinic REST アプリケーションに「ペットホテル機能」を追加することで、Issue Driven Development のワークフローと **GitHub Copilot カスタムエージェント** の活用方法を学びます。

**学習目標**:
- Issue Driven Development の実践
- GitHub Copilot カスタムエージェントの活用
- 開発工程ごとの適切なエージェント選択
- ブランチ戦略とGitワークフロー
- Pull Request の作成とコードレビュー

## 前提条件

- Java 17 以上
- Maven 3.9 以上（Gradleでもかまいませんが、説明はMavenで行います）
- Git
- GitHub アカウント
- VS Code （他のIDEでもかまいませんが、説明はVS Codeで行います）
- **GitHub Copilot サブスクリプション**

## エージェントベース開発フロー

このハンズオンでは、開発工程ごとに専門のエージェントを活用します：

| 工程 | エージェントロール | 主な役割 | ガイド |
|------|-------------------|---------|--------|
| 環境セットアップ | - | 環境構築、既存コード理解 | [Phase 0](guides/phase0-setup.md) |
| 要求開発 | プロダクトマネージャー (PM) | Issue作成、要件定義 | [Phase 1](guides/phase1-requirements.md) |
| 基本設計 | ソフトウェアアーキテクト | システム構造設計、エンティティ設計 | [Phase 2](guides/phase2-basic-design.md) |
| 詳細設計 | テックリード | API設計、データベース設計 | [Phase 3](guides/phase3-detailed-design.md) |
| 実装 | シニアデベロッパー | コード実装 | [Phase 4](guides/phase4-implementation.md) |
| 単体テスト | QA エンジニア | テストコード作成 | [Phase 5](guides/phase5-unit-test.md) |
| 統合・総合テスト | QA エンジニア/QA マネージャー | API動作確認、品質保証 | [Phase 6](guides/phase6-integration-test.md) |
| 受け入れ | リード | PR作成、レビュー、マージ | [Phase 7](guides/phase7-final-review.md) |

## 🎯 Issue Driven Development のポイント

**各工程（Phase）ごとにIssueとPRを作成します**：

1. **Issue作成**: 各工程の開始時に、Issue Formを使用してIssueを作成
2. **作業実施**: Issueで定義した内容を実施（設計書作成、コード実装など）
3. **PR作成**: 作業完了後、Pull Requestを作成してレビュー依頼
4. **承認・マージ**: レビュー承認後にマージし、Issueをクローズ
5. **次工程へ**: 前工程のIssue番号を参照して、次のIssueを作成

**メリット**:
- 🎯 各工程の目的と成果物が明確
- 📝 作業履歴が自動的に記録される
- 👥 工程ごとのレビューで品質向上
- 🔄 フィードバックループが早い

## 📚 ガイド構成

各フェーズの詳細ガイドは以下のファイルに分割されています：

### Phase 0: [環境セットアップ](guides/phase0-setup.md)
- リポジトリのフォークとクローン
- アプリケーションの起動確認
- 既存APIの確認
- 既存コード構造の理解

### Phase 1: [要求開発](guides/phase1-requirements.md)
- PM エージェントの活用
- Issue Form を使用した要件定義
- ユーザーストーリーの作成
- 要件定義書の作成
- PR作成と承認プロセス

### Phase 2: [基本設計](guides/phase2-basic-design.md)
- アーキテクトエージェントの活用
- エンティティ設計
- データベーススキーマ概要
- レイヤー構造の定義
- 設計ドキュメント作成とPR

### Phase 3: [詳細設計](guides/phase3-detailed-design.md)
- テックリードエージェントの活用
- データベーススキーマ詳細（DDL）
- REST API仕様
- DTO設計
- バリデーション仕様

### Phase 4: [実装](guides/phase4-implementation.md)
- シニアデベロッパーエージェントの活用
- Entity, Repository, Service, Controller の実装
- DTO と Mapper の実装
- 実装のベストプラクティス

### Phase 5: [単体テスト](guides/phase5-unit-test.md)
- QA エンジニアエージェントの活用
- テストケース設計
- MockMvc を使用したController テスト
- テストカバレッジの確認

### Phase 6: [統合テスト](guides/phase6-integration-test.md)
- QA マネージャーエージェントの活用
- API統合テスト
- エンドツーエンドテスト
- 品質保証レポート

### Phase 7: [最終レビューと振り返り](guides/phase7-final-review.md)
- リードエージェントの活用
- 全体レビュー
- 振り返り
- 追加課題

## 🚀 スタート方法

1. **[Phase 0: 環境セットアップ](guides/phase0-setup.md)** から開始してください
2. 各フェーズを順番に進めてください
3. 各フェーズ完了後、必ずPRを作成して承認を得てください
4. 次のフェーズに進む前に、前フェーズのIssueがクローズされていることを確認してください

## 📖 関連資料

- [HANDS_ON_README.md](HANDS_ON_README.md) - スタートガイド
- [QUICK_REFERENCE.md](QUICK_REFERENCE.md) - クイックリファレンス
- [INSTRUCTOR_GUIDE.md](INSTRUCTOR_GUIDE.md) - 講師向けガイド
- [AGENT_PROMPT_GUIDE.md](AGENT_PROMPT_GUIDE.md) - エージェント活用ガイド

## 🆘 困ったときは

### 1. QUICK_REFERENCE.md を確認
よく使うコマンドやパスがまとまっています。

### 2. 各フェーズのトラブルシューティング
各フェーズガイドの末尾にトラブルシューティングセクションがあります。

### 3. 講師に質問
わからないことは遠慮なく質問してください。

---

**それでは、[Phase 0: 環境セットアップ](guides/phase0-setup.md)から開始しましょう！**

Happy Coding! 🚀
