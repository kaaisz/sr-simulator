# プロジェクト規約

## 成果物
- index.html 単一ファイル（外部依存なし、CDN不可、ビルド不要）
- GitHub Pagesでそのまま公開できること

## 絶対条件
- このデモ自体がWCAG 2.2 AA準拠であること（皮肉なことに、a11yデモが
  アクセシブルでないのは致命的）
- prefers-reduced-motion / prefers-color-scheme に対応
- 実スクリーンリーダー（VoiceOver/NVDA）でも破綻なく操作できること
- 日本語/英語の完全バイリンガル（lang属性も動的に切替）

## 検証コマンド
- npx serve .            # ローカル確認
- npx axe http://localhost:3000 --exit   # 監査（違反0が合格）

## Git規約
- 生成・修正の各段階で必ずcommit（v1, v1-audit, v2, ...）
- コミットメッセージに「何のa11y違反を直したか」を書く