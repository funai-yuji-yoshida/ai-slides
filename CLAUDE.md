# CLAUDE.md — AI知識スライド集 制作ルール

AI知識のHTMLスライド集。GitHub Pages で公開（https://funai-yuji-yoshida.github.io/ai-slides/）。
スライドを追加・修正するときは、このルールに従う。

## 1. フォルダ構成

```
ai-slides/
├─ index.html        一覧ページ（slides.json を読み込んで表示。原則触らない）
├─ slides.json       一覧に載せるスライドのメタデータ
├─ readme.md         公開向けの説明
├─ .nojekyll         GitHub Pages 用（削除しない）
└─ slides/
   └─ <slug>/index.html   1スライド＝1フォルダ＋1ファイル
```

- スライドは `slides/<slug>/index.html` の **単一ファイル完結**。CSS・JS・SVG・画像（base64）をすべて埋め込む。ビルド工程は無い。
- `<slug>` は半角小文字とハイフン（例: `ai-security`, `stroop-effect`）。公開URLに使われるため、公開後は変更しない。
- 外部読み込みは Google Fonts のみ（例外: `ai-quest` は Tailwind CDN を使用。新規スライドでは使わない）。
- スライド間リンクは相対パスで書く（例: `../ai-pitfalls/#regression`）。

## 2. 新しいスライドを追加する手順

1. 既存スライドを1つ選び、構成の手本にする（操作バーありの標準型は `ai-data-levels`、固定ステージ型は `ai-security`、1ページ読み物型は `ai-pitfalls`）。
2. `slides/<slug>/index.html` を作る。デザインルール（§3）と表現ルール（§4）に従う。
3. `slides.json` に1件追加する（形式は下記）。
4. `readme.md` の「収録スライド」表に1行追加する。
5. §5 の公開前チェックを行う。
6. 既存スライドのファイルは、依頼がない限り変更しない。

### slides.json の形式

配列の先頭に近い位置に1オブジェクトを追加する。

```json
{
  "title": "スライドのタイトル",
  "description": "一覧に出す1〜2文の説明。",
  "path": "slides/<slug>/",
  "tags": ["基礎"],
  "updated": "YYYY-MM-DD"
}
```

- `path` は `slides/` で始まり、末尾は `/`。先頭に `/` や `./` を付けない。
- `updated` は `YYYY-MM-DD`。一覧は `updated` の新しい順に並ぶ。内容を更新したら日付も更新する。
- `tags` は配列。既存タグを優先して使う: `基礎` / `セキュリティ` / `人間の認知` / `AIの得意・不得意` / `診断`。新タグは必要なときだけ。
- 有効なJSONにする（末尾カンマ・コメント不可、文字コードUTF-8）。

## 3. デザインルール

### 配色
- 基調は **インディゴ＋イエローマーカー**。ライトとダークの両方を必ず用意する。
- 色は `:root` のCSSカスタムプロパティで定義し、直書きしない。

| 役割 | ライト | ダーク |
|---|---|---|
| 背景 `--bg` | `#F4F6F9` | `#12151F` |
| 面 `--surface` | `#FFFFFF` | `#1A1F2E` |
| 文字 `--ink` | `#1C2430` | `#E8EBF2` |
| 補助文字 `--sub` | `#5B6676` | `#9AA3B5` |
| 主色 `--indigo` | `#2B3A8C` | `#8C9BEA` |
| 主色の淡色 `--indigo-soft` | `#E3E7F6` | `#262E4C` |
| 強調マーカー `--mark` | `#F2C230` | `#F2C230` |
| 罫線 `--line` | `#D9DEE7` | `#2C3346` |
| 良い `--ok` / 悪い `--ng` | `#1F7A5A` / `#B3412F` | `#5BC79E` / `#E98572` |

- ダーク対応は次の2か所に同じ値を書く。
  - `@media (prefers-color-scheme: dark){ :root:not([data-theme="light"]){…} }`
  - `:root[data-theme="dark"]{…}`
- 扱うテーマ上、別の色が必要な場合（錯視・ストループ・診断など）は、そのスライド内だけで定義してよい。ただし文字と背景のコントラストは保つ。
- 色だけで意味を伝えない（○✕、ラベル、アイコンを併用する）。

### フォント
- 本文・見出し: **Zen Kaku Gothic New**（400/500/700/900）。フォールバックは `"Hiragino Kaku Gothic ProN","Yu Gothic",Meiryo,sans-serif`。
- コード・データ例: JetBrains Mono または IBM Plex Mono。
- 明朝・装飾書体は、題材に必要な場合だけ追加する。
- Google Fonts は `<link>` で読み込み、`display=swap` を付ける。

### レイアウト
- 先頭に `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`。`<html lang="ja">`。
- ノッチ対応: `padding-top/bottom: env(safe-area-inset-*)`。
- スライドの形式は次の2つから選ぶ。
  - **ステージ固定型**（16:9）: 固定サイズのステージ（1280×720 または 1600×900）を JS で拡縮して画面に収める。投影向け。
  - **フロー型**: ビューポート幅に追従するレスポンシブ。スマホで読む想定の内容向け。
- 1スライド1メッセージ。文字は投影でも読める大きさにし、情報を詰め込みすぎない。
- 体験（クリックで切り替わる表・クイズ・診断など）を入れる場合は、操作できる場所が分かる見た目にする。
- `prefers-reduced-motion` ではアニメーションを止める。`:focus-visible` のフォーカスリングを消さない。

### 操作バー（ステージ型スライド）
- 画面下部に固定。左から **一覧 / 目次 / 前へ / 進捗バー＋ページ数 / 次へ**（全画面ボタンは任意）。
- 操作方法はすべて対応させる:
  - キー: `←` `→` `PageUp` `PageDown`、`Space`（`Shift+Space` で戻る）、`Home` / `End`、`Esc`（目次を閉じる）、`F`（全画面）
  - タッチ: 左右スワイプ
  - URLハッシュ（`#3` で3枚目を開く）
- 最初のページでは「前へ」、最後のページでは「次へ」を `disabled` にする。
- 幅620px以下では「前へ」「次へ」の文字を隠し、矢印だけにする。
- 目次はスライドの `data-title` から自動生成する。`aria-expanded` を更新する。
- ボタンには `aria-label` を付ける。

### 一覧へ戻るボタン
- すべてのスライドに、一覧（`../../`）へ戻る導線を必ず付ける。
- 操作バーがあるスライドは、バーの左端に「一覧」ボタンを置く。
- 操作バーが無いスライドは、`</body>` の直前に次の共通部品を置く（`ai-counting`・`ai-quest`・`shepard-illusion`・`stroop-effect` と同じ）。

```html
<!-- 一覧へ戻るボタン（ai-slides共通） -->
<a href="../../" class="aislides-back" aria-label="スライド一覧へ戻る">← 一覧</a>
<style>
.aislides-back{position:fixed;top:calc(10px + env(safe-area-inset-top,0px));right:calc(10px + env(safe-area-inset-right,0px));z-index:9999;
  font:700 13px/1 "Zen Kaku Gothic New","Hiragino Kaku Gothic ProN","Yu Gothic",Meiryo,sans-serif;letter-spacing:.02em;
  color:#1C2430;background:rgba(255,255,255,.88);border:1px solid rgba(28,36,48,.18);border-radius:999px;padding:8px 14px;
  text-decoration:none;box-shadow:0 2px 8px rgba(0,0,0,.12);-webkit-backdrop-filter:blur(6px);backdrop-filter:blur(6px);opacity:.85;transition:opacity .2s}
.aislides-back:hover{opacity:1}
.aislides-back:focus-visible{outline:3px solid #F2C230;outline-offset:2px;opacity:1}
@media print{.aislides-back{display:none}}
</style>
```

- 読み物型は、ページ上部に `<nav class="topnav"><a href="../../">← スライド一覧</a></nav>` を置いてもよい。
- リンク先は必ず `../../`（絶対パス `/` は、GitHub Pages のサブパスで壊れるので使わない）。

## 4. 表現ルール

- **断定を避ける。** 「必ず」「絶対」「100%」「常に」は使わない。「〜とは限らない」「〜することがある」「〜という傾向がある」「〜の可能性がある」を使う。AIの能力・性能・リスクについて言い切らない。
- **根拠のない数値・出典を書かない。** 検証結果は、いつ・何を・どう試したかを添える。実在を確認できない論文・調査・URLを出さない。
- **事例は架空と明記する。** 説明のために作った事例・数値・データには、そのページ内に「架空の例」「架空のデータ」「研修用ダミー」などと必ず書く（例: 「本ページの事例・数値は、説明のための架空の例です。」）。実在の企業・個人・商品を匂わせない。
- **ダミーの秘密情報は `demo-` で始める。** APIキー・トークン・パスワード・社内情報の例は、`demo-key-7f3a…` のように必ず `demo-` で始め、本物に見える文字列（実在サービスのキー形式の完全な文字列など）を使わない。本物の秘密情報は、スライドにも画面キャプチャにも含めない。
- **実在の人名・研究を出すときは正確に。** 心理学の実験・研究者名などは、内容を単純化しすぎず、確認できる範囲で書く。
- **AIに入力するサンプルは、ダミーデータだけ。** 実際の顧客情報・社内データを使わない。
- **誰向けかを意識する。** 専門用語は、身近なたとえと具体例で説明する。AIを使い始めた業務担当者が読んで分かる文章にする。
- 文体は「です・ます」または体言止め。1スライドの文は短く。

## 5. 公開前のチェック項目

公開前に、次をすべて確認する。

### リンク切れ
- [ ] 一覧へ戻るボタンが `../../` を指し、押すと一覧ページに戻れる。
- [ ] スライド間リンク（例: `../ai-pitfalls/#regression`）の先とアンカー（`id`）が存在する。
- [ ] 外部リンクがある場合は、開いて確認する。出典URLは実在するものだけ。
- [ ] 一覧ページ（`index.html`）から、新しいスライドが開ける。
- [ ] 画像・フォントが読み込まれている（開発者ツールのコンソール／ネットワークにエラーが無い）。

### スマホ表示
- [ ] 幅375px前後（iPhone SE〜標準サイズ）で、横スクロールや文字のはみ出しが無い。
- [ ] 横向き・縦向きの両方で、操作バーとボタンが押せる。ボタンは指で押せる大きさ（目安44px）。
- [ ] スワイプでページ送りでき、縦スクロールの邪魔をしない。
- [ ] ノッチ・ホームバー付き端末で、バーが隠れない（`safe-area-inset`）。
- [ ] ライト／ダークの両方で、文字が読める。
- [ ] PCで `←` `→` キーと全画面表示が動く。

### slides.json の形式
- [ ] 有効なJSON（構文エラー・末尾カンマが無い）。
- [ ] 追加した1件に `title` `description` `path` `tags` `updated` がすべてある。
- [ ] `path` が `slides/<slug>/` の形で、フォルダが実在し、中に `index.html` がある。
- [ ] `updated` が `YYYY-MM-DD`。
- [ ] 一覧ページを開いて、新しいスライドが正しく表示される（タイトル・説明・タグ・更新日）。

### 内容
- [ ] 表現ルール（§4）に反する断定・実在を匂わせる事例・本物らしい秘密情報が無い。
- [ ] 架空の事例・データに「架空」と明記してある。
- [ ] 秘密情報の例は、すべて `demo-` で始まっている。
- [ ] `readme.md` の「収録スライド」表を更新した。

## 6. Git の扱い

- コミットは、依頼があったときだけ行う。メッセージは、何を追加・修正したかが分かる文にする。
- 既存スライドの内容は、依頼がない限り変更しない。
