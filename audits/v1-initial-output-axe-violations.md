# AIの初回出力（v1）に含まれていたaxe違反 — 定量データ

- 対象コミット: `5e1be2c`（v1: スクリーンリーダー体験シミュレータ初版実装, 2026-07-06T02:01:48+09:00）
- 生成モデル: Claude Fable 5
- 監査ツール: axe-core 4.12.1 / chrome-headless（Chrome 149.0.7827.201, ChromeDriver 149.0.7827.155）
- 修正コミット: `2d62bd6`（v1-audit, 2026-07-06T02:25:23+09:00）
- 本ドキュメント作成方法: `git show 5e1be2c:index.html` でv1時点のファイルを復元し、
  作業ツリーとは別ポートで再度サーブして実際にaxeを再実行し、数値を実測した
  （記憶や会話ログの要約ではなく、この文書作成時点での再現実行に基づく）。

## 結論（要約）

| 監査方法 | 検出された違反 |
|---|---|
| `npx axe <url>` によるCLI単純監査（初期表示のみ） | **0件** |
| 実際の操作を再現した5状態それぞれでの監査 | **1ルール種別 × 3状態 = 3インスタンス** |

CLIの単純な1回きりの監査では違反0だったが、これは偽陰性だった。シミュレータ画面に遷移して初めてDOMに現れる要素に、実際には未修正のランドマーク違反が1件存在していた。

## 検証した5状態と結果

| # | 状態 | 違反数 | ルールID | 深刻度 |
|---|---|---|---|---|
| 1 | イントロ画面（初期表示） | 0 | — | — |
| 2 | シミュレータ / BEFORE / ブラー中 | 1 | `region` | moderate |
| 3 | シミュレータ / BEFORE完了・種明かし後 | 1 | `region` | moderate |
| 4 | リザルト画面 | 0 | — | — |
| 5 | シミュレータ / BEFORE / 英語表示 | 1 | `region` | moderate |

検証した5状態のうち3状態（60%）で同一ルールの違反が検出された。ユニークなルール種別は1つ（`region`）。

## 違反の詳細

- **ルール**: `region` — "All page content should be contained by landmarks"
- **該当要素**: `<p class="bar-label">読み上げ字幕</p>`（画面下部に固定表示される読み上げ字幕バーのラベル。英語表示時は `Speech captions`）
- **セレクタ**: `.bar-label`
- **タグ**: `cat.keyboard`, `best-practice`, `RGAAv4`, `RGAA-9.2.1`
- **helpUrl**: https://dequeuniversity.com/rules/axe/4.12/region?application=axeAPI

### 原因

固定表示の字幕バー（`#captionbar`）自体にランドマークロールが付与されておらず、内部の "読み上げ字幕" というテキストがどのランドマーク（`header`/`main`/`nav`/`footer`/`role="region"`等）にも属さない状態でDOMに存在していた。

### なぜCLIの初期表示監査では検出されなかったか

字幕バーは `hidden` 属性によりイントロ画面では非表示になっている。「体験をはじめる」ボタンを押してシミュレータ画面に遷移すると、JSが `hidden` を外して初めてDOM上に出現する。axeは非表示要素を評価対象から除外するため、単一URLへの初回ロードのみを見るCLI監査（`npx axe http://localhost:3000 --exit`）ではこの要素が監査範囲に含まれていなかった。

## 修正内容（コミット `2d62bd6`）

```diff
-<div class="captionbar" id="captionbar" hidden>
+<div class="captionbar" id="captionbar" role="region" aria-label="読み上げ字幕" data-i18n-arialabel="captionLabel" hidden>
```

加えて、言語切替時にも `aria-label` が追随するよう、`data-i18n-arialabel` 属性を汎用的に処理する数行を `renderLang()` に追加した。

修正後、同じ5状態を再監査し、**全状態で violations: 0** を確認した。

## この事例からの示唆

1. **単発のCLI監査はSPA的なUIの偽陰性を生む。** 状態遷移で初めてDOMに現れる要素は、初期表示だけを見る監査では見逃される。今回はイントロ画面が0件だったために、もし多状態検証をしていなければ「axe違反0」を誤って報告していた可能性がある。
2. **この違反は、デモが意図的に再現している「BEFOREモードの5つの不備」（フォーカス順序、aria-label欠落、シークバー到達不能、状態非伝達、機械的alt）とは別物である。** それらのモック要素には `aria-hidden="true"` が付与されており、そもそも支援技術にもaxeにも露出しない疑似コンテンツである。今回axeが検出したのは、シミュレータの「土台」側（実DOMのライブリージョン運用部分）に実際に存在した、意図しない欠陥だった。
3. **CLAUDE.mdが要求する「axe違反0」は、AIによる初回生成（v1）の時点では満たされていなかった。** 複数状態を明示的に操作して検証して初めて欠陥が可視化され、1回の追加修正コミットで解消された。

## 参考: 生データ（axe-core出力抜粋）

<details>
<summary>state 2 (BEFORE, blurred, ja) の violations 全文</summary>

```json
{
  "id": "region",
  "impact": "moderate",
  "help": "All page content should be contained by landmarks",
  "helpUrl": "https://dequeuniversity.com/rules/axe/4.12/region?application=axeAPI",
  "tags": ["cat.keyboard", "best-practice", "RGAAv4", "RGAA-9.2.1"],
  "nodes": [
    {
      "target": ".bar-label",
      "html": "<p class=\"bar-label\" data-i18n=\"captionLabel\">読み上げ字幕</p>",
      "failureSummary": "Fix any of the following:\n  Some page content is not contained by landmarks"
    }
  ]
}
```

</details>
