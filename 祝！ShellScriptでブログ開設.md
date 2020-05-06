:title 祝!ShellScriptでブログ開設
:create 2020-05-06 14:14:46
:create 2020-05-06 13:27:08
:create 2020-05-06 13:26:20

:body

自粛期間でGWは暇だったのとbashCMSに影響を受けてShellScriptでブログを作成しました。  
  
### インフラ周り
AWS EC2でnginxを動かしています。ドメインはroute53で  
取得しました。自作コマンドのbpostでblog記事の投稿はscpでサーバに直接アップロードしています。  
バックアップとしてblog投稿時にgithubにあるリポジトリへpushしています。  
  
### blog本体
すべてShellScriptで作成しています。cgiのqueryを取得する部分は  
秘密結社シェルショッカー 日本支部様がPOSIXの範囲で実装したOpen-usp-Tukubaiの  
cgi-name,namereadコマンドを使用しています。markdownをサポートしており  

```
:title test
:create 2020-01-01 00:00:00

:body

テスト
```

上記の書式でブログを投稿します。投稿時間はbpostで投稿する際に自動で追記されます。  
markdownからhtmlへ変換する部分は自作コマンドのmd2htmlを使用して変換しています。  
  
比較的楽に実装することができました。互換性を意識してShellScriptを書きましたが  
テストしているわけではないので動かない環境もあるかも知れません。  
ShellScriptでこれからもコードを書いていきたい。