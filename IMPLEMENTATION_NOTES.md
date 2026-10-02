# 実装の確認メモ

確認日：2026年10月2日

## 確認の範囲と限界

作品一覧から案内される公開リポジトリのソースコードを確認しました。ブラウザでの通信の実測、Google Analyticsの管理設定、外部サービス側のログや保持期間は確認していません。公開ソースと配信中の内容の一致を全ページで検証したものではありません。

この確認では、入力した文章やファイルを運営者のサーバーにまとめて保存する処理は見当たりませんでした。ただし、「外部通信が一切ない」「入力情報は一切端末外へ出ない」という説明は現在のコードに合いません。

## 保存先

- 保存機能のあるツールではlocalStorageやIndexedDBを使用します。
- receipt-pdf-appはDexieを介したIndexedDBを使用します。
- 定時アナウンスツールでは音声データの保存にIndexedDBを使用します。
- 一部の画像・CSV・文章などのツールはメモリ上のみで処理し、永続保存を行いません。
- 同じオリジンで公開されるツールでは、ブラウザの保存領域はURLのパスごとに完全に独立するものではありません。

## 外部通信と共有

### Google Analytics

作品一覧ページにGoogle Analyticsのタグがあり、測定IDはG-58EVYNZJVBです。ページ内には解析タグの読み込みを利用者の同意後に限定する処理は見当たりませんでした。

根拠：[portfolio/index.html](https://github.com/humandoll26/portfolio/blob/main/index.html#L1067)

### 名もなき本棚

書籍情報の取得でISBNをopenBD APIに送信します。共有URLを開いた際もISBNから書籍情報を取得する処理があります。コメントをopenBDへ送信する処理は見当たりませんでした。

現在の共有URLには本棚のタイトルとISBNを含めます。Amazon・バリューブックスの検索リンクを利用すると、ISBNまたは書籍名が各リンク先へ渡ります。

根拠：[共有データ](https://github.com/humandoll26/My_Shelf/blob/main/script.js#L485)、[ISBN取得](https://github.com/humandoll26/My_Shelf/blob/main/script.js#L925)、[検索リンク](https://github.com/humandoll26/My_Shelf/blob/main/script.js#L1109)

### トーナメント管理ツール

大会名、参加人数、参加者、対戦情報・結果などの状態を共有URLのクエリに含めます。Base64などで文字列を変換していますが、暗号化ではありません。

本棚・トーナメントの共有URLを開くと、クエリを含むURLが配信先に送信されます。URL生成・コピー自体はサーバーへのアップロードではありません。

根拠：[保存する状態](https://github.com/humandoll26/tournament_manager/blob/main/index.html#L849)、[共有URL生成](https://github.com/humandoll26/tournament_manager/blob/main/index.html#L1072)

### 音声読み上げ

よむね・定時アナウンスツールはWeb Speech APIを使用します。音声をlocalServiceがtrueのものだけに制限していません。ブラウザ・OS・選択音声によっては読み上げ文章が外部の音声サービスへ送信される可能性があります。実際の転送を測定した結果ではありません。

根拠：[よむね](https://github.com/humandoll26/txt-reading-tool/blob/main/index.html#L714)、[定時アナウンス](https://github.com/humandoll26/teiji-announcement-tool/blob/main/index.html#L857)、[Web Speech API仕様](https://webaudio.github.io/web-speech-api/#dom-speechsynthesisvoice-localservice)

### ページや素材の取得

GitHub Pagesへのページ要求のほか、ツールによってjsDelivr、cdnjs、Google Fontsなどの素材配信先にアクセスします。指定した外部画像の取得や、HTMLのプレビューに含めた外部素材の取得も発生する場合があります。

仕入メモの更新確認やメルカリCSVツールのマスターデータ取得など、同じ公開サイトへの取得通信もあります。これらの処理でメモ本文や作成した商品情報を送信するコードは見当たりませんでした。

GitHub Pagesでは安全確保のためアクセス元IPアドレスが記録されます。

根拠：[GitHub Pagesの説明](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages)

### お問い合わせ・外部サービス

外部の問い合わせフォームやメールを利用して連絡すると、利用者が送信した内容はそのサービス・受信者へ渡ります。外部販売サイト、SNS、ファイル変換サービスなどは個別のプライバシーポリシーの対象です。

演劇ECサンプルの決済先は確認時点でプレースホルダーです。実際の決済が稼働しているとは確認できませんでした。

## 運用時に揃える説明

- 「ブラウザ内で処理・保存すること」と「外部通信がないこと」を区別する。
- 音声読み上げは端末内処理だけとは断定しない。
- 共有URLは暗号化されていないことと、共有対象の項目を説明する。
- アクセス解析、ISBN検索、素材読み込みの外部通信を説明する。
- ポリシーを掲載しても、SmartScreenやSafe Browsingの警告解除が保証されるものではない。
