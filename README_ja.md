# ITR DELTA - Custom 行マニュアル

[English (README.md)](README.md)

**ITR DELTA** は Garmin ウォッチ用のドットマトリクス文字盤です。丸い画面全体が 5x7 のドット文字の格子になっていて、そのうち 9 行に、自分で書いた文字列を表示できます。時刻、日付、GMT 時刻、心拍数、歩数、Body Battery、SpO2、ゲージ、履歴グラフ、横幅 2 倍や縦横 2 倍の大きな文字、簡単な計算式まで使えます。

> この文字盤は極めて自由にカスタマイズできます。どんなデザインも可能です。(THIS WATCHFACE IS EXTREMELY CUSTOMIZABLE. ANY DESIGN IS POSSIBLE.)

- Garmin Connect IQ で入手: [ITR DELTA](https://apps.garmin.com/apps/4bc2191c-0fc0-4380-b7a0-36d3c6907dc3)
- デザインギャラリー: **[gallery](https://developitrjp.github.io/itr_delta_custom_line/gallery.html)** - チェッカーで作ったデザインの一覧です。気に入ったものをチェッカーで開いて、自分用に変えられます
- Custom 行の書き方: **[CUSTOM_STRING_ja.md](CUSTOM_STRING_ja.md)**（コード、ゲージ、グラフ、WIDE / BIG、揃え、使用例、初期設定）。英語版: **[CUSTOM_STRING.md](CUSTOM_STRING.md)**

## AI に Custom 設定を書いてもらう

マニュアルを AI アシスタント（Claude、ChatGPT、Gemini など）に渡して、行のデザインを頼めます。

1. AI にマニュアルを渡します。[CUSTOM_STRING_ja.md](CUSTOM_STRING_ja.md) の全文を貼り付けるか、raw リンク `https://raw.githubusercontent.com/developitrjp/itr_delta_custom_line/main/CUSTOM_STRING_ja.md` を渡します（リンクを開けない AI もあります。その場合は文章を貼り付けるか、ファイルを添付します）。
2. どの行に何を表示したいかを伝えます。例:
   - 「このマニュアルを使って、ニューヨーク時刻（例: NY 22:32）を表示する Custom3（15 文字）を書いてください。」
   - 「Custom7 に心拍グラフと気圧グラフを並べて表示してください。」
   - 「ランニング向けのデザインで 9 行すべてを埋めてください。大きな時刻、心拍数、歩数、バッテリーを入れてください。」
3. AI が出した文字列（コードブロックの中身）を、Garmin Connect の文字盤の Custom 設定に貼り付けます。Garmin Connect > 文字盤 > 設定 > Custom1〜Custom9。新しい設定項目が出ないときは、Garmin Connect を完全に終了してから開き直してください。
4. 時計で結果を確認します。AI には時計が見えません。表示がおかしいときは、見えているものと行番号を AI に伝えて、文字列を直してもらってください。時差の行は GMT オフセットの設定に、AM/PM は時計の 12 / 24 時間表示の設定に依存します。

マニュアルには、AI が守る規則（行の幅、よくある間違い、出力の形式）をまとめた「AI アシスタントへ」の節があります。

## ブラウザで文字列を試す

**[Custom row checker](https://developitrjp.github.io/itr_delta_custom_line/custom_checker.html)**: 1 枚の Web ページ（`custom_checker.html`。ダウンロードしてブラウザで開けば、オフラインでも使えます）です。9 つの Custom 文字列を入力し、揃えを選ぶと、時刻・心拍数・歩数などのサンプル値で、文字盤にどう表示されるかをおおよそ確認できます。長すぎる文字列、表示できない文字、4 個を超える GRAPH の警告も出ます。簡易的な確認で、実機の描画そのものではありません。

デザインの共有: 入力した内容はページのアドレスに保存されるので、**Copy share link** を押すと、同じデザインを誰でも開ける URL が得られます。**Download PNG** はプレビューを保存します。**Post to Discussions** は、このリポジトリの [Discussions](https://github.com/developitrjp/itr_delta_custom_line/discussions) に、デザインを入力した新規投稿を開きます（PNG はそこに添付してください）。投稿を眺めてアイデアを探したり、自分のデザインを共有したりできます。

**[デザインギャラリー](https://developitrjp.github.io/itr_delta_custom_line/gallery.html)**: デザインの一覧ページです（`designs.json` から、ブラウザ上で描画します）。「Open in checker」でそのデザインをチェッカーに読み込めます。各行の **Copy** ボタンは、その行の設定をコピーするので、Garmin Connect にそのまま貼り付けられます。

**デザインコード**: **Copy design code**（チェッカーとギャラリー）は、デザイン全体（9 行の内容、揃え、色）を 1 つのコードとしてコピーします。Garmin Connect の文字盤の最後の設定 **Design code** に貼り付けて保存すると、次の同期のあと、すべてが設定されます。チェッカーの **Load code** 欄は、コードをチェッカーに読み込みます。

このリポジトリにあるのはマニュアルだけで、文字盤のソースコードは公開していません。

## サポート

ITR DELTA が役に立ったら、開発を支援していただけます: [Buy Me a Coffee](https://buymeacoffee.com/develop.itr.jp)

## ライセンス

`LICENSE` を参照してください。
