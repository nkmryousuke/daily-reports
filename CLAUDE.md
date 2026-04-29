# 日次投資レポート生成ルール

このリポジトリで `daily_investment_report_YYYYMMDD.html` を生成/更新するときは、以下のルールに従うこと。

## 基本情報

- **主な参照元**: https://kabutan.jp/
- **更新時刻**: 毎営業日 16:00 JST（平日のみ、土日は生成しない）
- **出力先**: `daily_investment_report_<YYYYMMDD>.html`（リポジトリルート）
- **テンプレート**: 前営業日の最新HTMLファイルを複製して使う
- **公開URL**: https://nkmryousuke.github.io/daily-reports/

## レポート構成（6セクション）

### ① 一般に大きなニュース 3つくらい（sec1）

- どの大手新聞社やニュースサイトでも出す**大きなニュース**を3つ選ぶ
- 各ニュースについて **解説** と **考えられる経済・市場への影響** をまとめる
- **急騰した個別株の解説はここには入れない**（それは③④⑤で扱う）
- マクロ・政策・地政学・主要指標・大企業の重大発表などが該当

### ② 投資家が見るべきニュース 3つくらい（sec2）

- 大きなニュースではないが、**投資家として見逃してはいけない**情報を3つ
- 各ニュースについて **解説** と **考えられる経済・市場への影響** をまとめる
- **急騰した個別株の解説はここには入れない**
- セクター動向・政策の細部・テーマ株の地合い・海外マーケット示唆などが該当

### ③ 直近営業日の値上がり銘柄 上位20（sec3）

- **参照元**: kabutan の値上がりランキング
- **各市場別に上位20銘柄**を取得（全東証 / プライム / スタンダード / グロース の4タブ）
- 各銘柄に以下を併記:
  - **値上がりしている理由・材料**（特定できるものは必ず特定する）
  - **種別タグ**: 以下のいずれかを必ず付ける
    - `news` 📰 材料あり — 適時開示・業績修正・大型受注・提携などの明確な材料
    - `momo` 📈 モーメンタム — テーマ性による買い継続
    - `heat` 🔥 過熱調整 — 前日までの急騰反動
    - `shide` 🚨 仕手株的 — 低位株の投機的な値動き、明確な材料なし
- **株価はダブルチェックする**（Yahoo!ファイナンスと kabutan の両方で確認し一致させる）

### ④ 直近営業日の値下がり銘柄 上位20（sec4）

- **参照元**: kabutan の値下がりランキング
- **各市場別に上位20銘柄**を取得（全東証 / プライム / スタンダード / グロース の4タブ）
- 各銘柄に以下を併記:
  - **値下がりしている理由・材料**（特定できるものは必ず特定する）
  - **種別タグ**: ③と同じタグを必ず付ける
- **株価はダブルチェックする**

### ⑤ 投資材料となるニュース（sec5）

- **参照元**: https://kabutan.jp/news/marketnews/?category=2 （市場ニュース → 材料タブ）
  - 取得時は `https://s.kabutan.jp/market_news/?category_org_id=2` が安定（403回避）
- **期間**: 前日 15:30 〜 当日 15:30 に出たニュース
- **スクリーニング基準**: 会社に大きな変化をもたらしそうなニュースを選ぶ
  - 業態転換・新規事業参入
  - 新工場建設・設備投資
  - 大型M&A・資本業務提携・子会社化
  - 大型受注
  - 業績上方修正（構造変化を伴うもの）
- **対象市場**: **プライムまたはスタンダードのみ**（グロースや名証は除外）
- **件数**: 10件程度
- 各ニュースの詳細ページ（`https://s.kabutan.jp/news/nXXXXXXXXXX/`）を開いて、**銘柄コード・社名・本文・発表時刻**を取得
- 各カードに以下を記載:
  - 事業内容
  - ニュース内容
  - 狙い・目的
  - 影響（関連銘柄への波及含む）
- 各カードに **🔗 元記事（株探）** リンクを必ず付与

### ⑥ 注目の決算（sec6）

- **対象**: 当日・前後に発表された注目決算（プライム・スタンダード中心、8〜10社程度）
- **データソース**: 株探の決算速報 `https://s.kabutan.jp/market_news/?category_org_id=3`（当日分はpage=1〜2、当日日付でフィルタ）
  - 四季報オンラインはログイン必須のため利用不可。代替として株探 category_org_id=3 を使う
  - 月次売上データも同ページに混在する場合あり（タイトルに「月次」「前月比」を含む記事）
- **各カードに記載する情報**:
  - 銘柄コード・社名・市場・対象期間（例: 26年3月期通期 / 26年12月期1Q）
  - **結果タグ**: 以下のいずれかを付ける
    - `beat` ▲ 上振れ — 会社計画比でも上振れ着地
    - `miss` ▼ 下振れ — 計画比で下振れ、またはガイダンス引き下げ
    - `rev-up` ↑ 上方修正 — 期中の上方修正発表
    - `rev-down` ↓ 下方修正 — 期中の下方修正発表
    - `inline` → 計画通り — ほぼ計画線
  - **財務テーブル**: 売上高・営業利益・純利益（または業務純益）の3行
    - 実績（または修正後予想） / 前年比 / 会社計画比
  - **コメント**: 数字の背景・投資家への示唆・関連銘柄への波及を1〜2文
- **全件掲載**: その日の決算速報をすべて `EARNINGS_ALL` に収録（フィルタ表示）
- **詳細カード**: 修正幅≥20% or 株価インパクト大 or 業界代表格の上位8件程度を `EARNINGS` に収録
- **フィルタボタン**: 全件／上振れ／上方修正／下振れ／下方修正 の5タブを実装

### 書き換え対象（sec6分追加）

- `const EARNINGS = [...]` 全体を新日付データで置換
- サイドバー nav の `nav-badge earn` の件数を実際の社数に合わせる

## HTML生成ルール

### テンプレート運用

1. `ls daily_investment_report_*.html | sort | tail -1` で最新ファイルを特定してテンプレートとする
2. `cp <最新> daily_investment_report_<YYYYMMDD>.html` で複製
3. 新しいデータで以下を書き換え

### 書き換え対象

- `<title>` および日付表示箇所すべて
- トップバーの日経平均（終値・前日比・前日比率）と前日終値表示
- 大ニュース4カード（sec1）
- 投資家ニュース4カード（sec2）
- `const STOCKS = {...}` 全体
  - `up.all` / `up.prime` / `up.std` / `up.growth`
  - `down.all` / `down.prime` / `down.std` / `down.growth`
  - 各20銘柄、実価格・実騰落率
- 材料ニュース10カード（sec5）
- 注目決算（sec6）の `const EARNINGS = [...]` 全体
- サイドバー nav の各バッジ数字（mat / earn）
- `const REPORT_DATES = new Set([...])` に新日付を追加
- `const EARNINGS = [...]`（sec6）
- `const TODAY_STR = '<YYYY-MM-DD>'`
- `const todayDate = new Date('<YYYY-MM-DD>')`
- フッターの生成日時

### スタイル規約

- 重要語は `<strong>` タグで強調（黄色ハイライト＋太字）
- 銘柄名には `https://kabutan.jp/stock/?code=<CODE>` のリンクを埋め込む
- 材料ニュースの元記事リンクは `https://s.kabutan.jp/news/nXXXXXXXXXX/` 形式

### カレンダー連携

- `REPORT_DATES` Set にその日の日付を追加すれば、サイドバーのカレンダーに自動でリンクが埋め込まれる
- 既存の全HTMLファイルの `REPORT_DATES` にも新日付を追加する（相互リンクのため）

## 更新後のファイル処理

### 1. 既存HTMLファイルの REPORT_DATES 更新

```bash
grep -l 'REPORT_DATES' daily_investment_report_*.html
```

でヒットした全ファイルの `const REPORT_DATES = new Set([...])` に新日付を追加（既存なら何もしない）。

### 2. index.html 更新

`index.html` の `const reports = [...]` 配列の**先頭**に新エントリを追加:

```js
{ date: '<YYYY-MM-DD>', label: '<YYYY年M月D日（曜）>' },
```

曜日は 月 / 火 / 水 / 木 / 金 のいずれか。

### 3. Git commit & push

```bash
git config user.email "bot@daily-reports"
git config user.name "daily-reports-bot"
git add .
git commit -m "Add report for <YYYYMMDD>"
git push origin main
```

## データ取得URL

### Yahoo!ファイナンス（値上がり/値下がりランキング）

| 市場 | 値上がり | 値下がり |
|---|---|---|
| 全東証 | `ranking/up?market=all&term=daily` | `ranking/down?market=all&term=daily` |
| プライム | `ranking/up?market=tokyo1&term=daily` | `ranking/down?market=tokyo1&term=daily` |
| スタンダード | `ranking/up?market=tokyo2&term=daily` | `ranking/down?market=tokyo2&term=daily` |
| グロース | `ranking/up?market=tokyoM&term=daily` | `ranking/down?market=tokyoM&term=daily` |

ベース: `https://finance.yahoo.co.jp/stocks/`

### 日経平均

`https://finance.yahoo.co.jp/quote/998407.O`

### 株探

- 材料ニュース一覧: `https://s.kabutan.jp/market_news/?category_org_id=2`（ページ 1〜5 を WebFetch）
- 銘柄ページ: `https://kabutan.jp/stock/?code=<CODE>`
- 個別ニュース: `https://s.kabutan.jp/news/n<YYYYMMDDXXXX>/`

### 市場マップ（STOCKS オブジェクトの `market` フィールド）

- プライム → `prm`
- スタンダード → `std`
- グロース → `grt`
- 名証・その他 → `other`

## エラー処理

- Yahoo!ファイナンス取得失敗時は3回リトライ
- kabutan.jp が 403 の場合は `s.kabutan.jp` を優先利用
- `git push` 失敗時はエラー詳細を出力し終了コード1

## 土日の扱い

- `TZ='Asia/Tokyo' date +%u` が `6` または `7` の場合は生成をスキップ
- 祝日判定は未実装（祝日に動くと前営業日分のデータで生成される）
