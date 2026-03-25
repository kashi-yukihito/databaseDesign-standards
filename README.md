# databaseDesign-standards
PostgreSQL向けDB設計・設定標準です

## 概要
本規約は、PostgreSQL v17.0以後を基準とし、以降のバージョンにも適用可能な設計指針を定めたものです。
長年の実務経験（DOA思想）に基づき、堅牢でメンテナンス性の高いデータ構造の構築を目的としています。

### 本規約のコンセプト/特徴
* **データ中心アプローチ (DOA)**: アプリケーションの都合に左右されない、本質的なデータ構造の定義
* **最新機能への追随**: PostgreSQL v17.0 までの主要な改善点の反映
* **実務経験の反映**: 過去の障害事例や運用知見に基づく、地に足の着いた制約定義

### 本書の想定適用対象
* 対象：OLTP中心の業務システム
* 想定規模：中〜大規模

## 構成
- [DatabaseDesignStandard.md](DatabaseDesignStandard.md): 日本語版の設計標準
- [DatabaseDesignStandard_EN.md](DatabaseDesignStandard_EN.md): 英語版の設計標準

## 利用例（スニペット・テンプレート）
- [CREATE TABLEテンプレート](Snippet-CreateTable.sql)
- [postgresql.conf.basic](postgresql.conf.basic): 基本設定テンプレート

## 備考
* 2008年頃に個人で作成していたPostgreSQL v8.0向けの設計規約をベースに生成AIを活用して最新のPostgreSQL v17.0に対応させたものです。
* 内容検討にあたり個人ブログで公開していた様々な記事を規約に昇華させています。
Reference: [つれづれネット散歩](http://kashi.way-nifty.com/jalan/)
* 本規約は、PostgreSQLのバージョンアップに伴い更新される可能性があります。
* 旧版（2008年頃）の設計規約は参考資料としてリポジトリに含まれています。
  - [DatabaseDesignStandard_legacy.md](DatabaseDesignStandard_legacy.md)

## AI活用について
本規約は、複数のAIツール（Claude, ChatGPT, Gemini, DeepL）の支援を受けて開発されました。 
AIを活用した詳細な設計作業の知見や共同開発プロセスについては、以下のドキュメントを参照してください。    
* [AI-assisted Development Insights (AI活用による知見メモ)](AI-INSIGHTS.md)

## License
本プロジェクトはMITライセンスのもとで公開されています。詳細は [LICENSE](LICENSE) を参照してください。
© 2026 かっしい