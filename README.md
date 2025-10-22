# mixik21.infoサーバー構成

#＃ 構成の見通し
Docker composeで4つのコンテナを立てる
* nginxコンテナ
* Next.jsコンテナ
* Pythonコンテナ
* MySQLコンテナ

### nginxコンテナ
- Next.jsで作ったサイトをビルドしたものを静的配信する
	- ReactのSPAなので配信するのは /index.html と各種 js のみ
	- そのほかの記事等は必要に応じて取得
- 記事はHTML形式 (Markdownにしてもいいかも)でアップロード
- 記事を追加するたびにビルドはしない (大幅改修時のみ)
- /api みたいなエンドポイントへのアクセスをPythonコンテナ (FastAPI)側にリバースプロキシ
- それ以外の見つからないファイルへのアクセスはNext.jsコンテナにリバースプロキシ
*備考*
- 開発用には80番ポートのみ開放している
- デプロイするときは認証鍵の登録と443番ポートの開放をして80番ポートへのリクエストは拒否するようにする

### Next.jsコンテナ
- Next.jsの静的配信できないやつ

### Pythonコンテナ
- FastAPI + uvicornでAPIを提供
	- 記事を入れておく用のディレクトリに入っているファイル一覧を返す (or 事前に作っておいたリストを返す: 作成日時とかタグとかの情報も含めるためにはこちらが良さそう？) なんならSQLでも良いかも？
		- SQLの場合、ID、タイトル、作成日時、タグ、サムネイルパス、本文テキストを入れておく？
		- 一覧ページの表示用API (IDとタイトル、作成日時、タグのみを返す)と、本文表示用API (IDと本文テキストを返す)で分ける？
		- これを作る場合、手動でやる or 新しいファイルが来たことを検知するシステム or ファイルチェック用ツールを作る？
	- 天気を取得するAPI？？

### MySQLコンテナ
- 追加した記事のID、タイトル、作成日時、タグ、サムネイルパス、本文テキストを入れておく
- 本文はNext.jsでMDXとして作ったものをそのまま入れておく？

*クレジット*
アイコン https://svgsilh.com/ja/9c27b0/

### 改良案
- 強調する外部リンクで、リンク先のアイコンとサムネイルを参照して表示する
- プルダウン (ボタン押したら開くやつ)用のクラスを作る (感想とか書くため)
- ご意見ご要望フォームを作る？
- tailwindCSSってやつでいい感じにレイアウトできるらしい

- Xのポストの埋め込みができない問題
  - `dangerousInnnerHTML`みたいなやつで読み込んだ<script>は実行されないらしい
```tsx
'use client'
import { useEffect } from 'react';

export function TweetEmbed({ id }: { id: string }) {
  useEffect(() => {
    window.twttr?.widgets?.load();
  }, []);

  return (
    <blockquote className="twitter-tweet">
      <a href={`https://twitter.com/twitter/status/${id}`}></a>
    </blockquote>
  );
}
```
からの
```mdx
<TweetEmbed id="1234567890123456789" />
```
は天才かも (Youtubeタグとかもいけそう)
