# maguro-robata-naru.com

「マグロと炉端 成る」（錦糸町・海鮮炉端）の公式サイトです。
既存の WordPress サイト（https://maguro-robata-naru.com）の文章・写真をもとに、GitHub Pages で公開できる静的 HTML/CSS/JS として作り直しています。

## 構成

```
index.html                トップページ（ヒーロー / こだわり / お食事 / お飲み物 / 席・空間 / お知らせ / Instagram / 店舗情報）
story/index.html          お店を作った経緯
concept/index.html        こだわり
course/index.html         コース（成るコース・贅沢コース・飲み放題・キャンセルポリシー）
food/index.html           お食事メニュー
drink/index.html          お飲み物メニュー
table/index.html          席・空間
group/index.html          団体情報
special/index.html        公式サイト限定特典
sister-store/index.html   姉妹店紹介
media/index.html          メディア掲載実績
information/index.html    お知らせ一覧
privacy/index.html        個人情報保護方針
css/style.css             共通スタイル（外部フレームワーク不使用・Google Fonts の Noto Sans JP / Noto Serif JP のみ）
js/main.js                ハンバーガーメニュー、サブメニュー、ページトップ、スクロール表示
images/                   ロゴ・写真（既存サイトから取得、ファイル名は半角英数字に変更）
site.config.json          店名・住所・電話・営業時間・各種 URL
CNAME                     GitHub Pages の独自ドメイン設定（変更しない）
```

- 全ページで同じヘッダー（ロゴ / グローバルナビ / WEB予約ボタン）とフッター（店舗情報 + Google マップ / 姉妹店舗ブランド / 限定特典バナー / 採用情報 / 個人情報保護方針）を使用しています。
- スマホ幅では画面下に「電話 / 地図 / WEB予約」の固定バーを表示します。
- リンクはルート相対（`/concept/` など）です。独自ドメイン直下で公開する前提のため、サブディレクトリ配信には対応していません。
- 予約は食べログネット予約（https://yoyaku.tabelog.com/yoyaku/net_booking_form/index?rcd=13255968）へリンクしています。
- 多言語切替（GTranslate）、Instagram フィード埋め込み、Google レビュー表示は再現していません（Instagram は「フォロー」ボタンのみ）。

## 更新のしかた

- 営業時間・住所などは各ページのフッター（`<section class="info">`）と `site.config.json` を合わせて更新してください。
- お知らせは `information/index.html` に `<article class="notice" id="nXXXX">` を追加し、トップページ `index.html` の「お知らせ」リストを直近 3 件に差し替えます。
- メニュー価格は `food/index.html` / `drink/index.html` の `<ul class="price-list">` を編集します（税込表記）。

## ローカルで確認

```bash
python -m http.server 8000
```

ブラウザで http://localhost:8000 を開きます。

## 公開までの流れ

1. GitHub にこのリポジトリを push する
2. Settings → Pages で `main` ブランチ / ルートを公開元に設定する
3. ドメイン管理側で DNS を GitHub Pages に向ける（A レコード 4 件 + www の CNAME）
4. Pages 設定で "Enforce HTTPS" を有効にする

## 評価表示（トップページの Google / 食べログ）

- 表示データは `data/ratings.json`。トップページの `js/ratings.js` が読み込んで評価帯（`#ratings`）に表示する。
- **Google**: `.github/workflows/update-ratings.yml` が毎日 03:00（JST）に `scripts/update_google_rating.py` を実行し、Places API から点数・口コミ件数を取得して `data/ratings.json` を更新・コミットする。リポジトリの Secrets に `GOOGLE_MAPS_API_KEY` が必要。初回実行時に `google.query` で店舗を検索して `place_id` を保存する（`matched_name` / `matched_address` で正しい店舗か確認する）。
- **食べログ**: 自動取得は利用規約上できないため手入力。`tabelog.score` / `tabelog.count` / `tabelog.asof` を店舗ページを見て月 1 回程度更新する。
- 点数が未取得（null）の間は、その項目は表示されない。
